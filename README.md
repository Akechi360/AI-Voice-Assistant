# AI-Voice-Assistant
import speech_recognition as sr
import nltk
from textblob import TextBlob
import pyttsx3
import openai

# Inicializar el motor de síntesis de voz
engine = pyttsx3.init()

# Configurar opciones de voz (opcional)
voices = engine.getProperty('voices')
engine.setProperty('voice', voices[0].id)
engine.setProperty('rate', 150)

# Configurar la API Key de OpenAI
openai.api_key = "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"

# Establecer el idioma
language = 'es-ES'

# Definir una función para procesar los comandos de voz del usuario
def process_command(command):
    # Utilizar TextBlob para analizar el comando de voz del usuario
    blob = TextBlob(command)
    
    # Utilizar NLTK para extraer cualquier entidad nombrada relevante
    entities = nltk.chunk.ne_chunk(nltk.pos_tag(nltk.word_tokenize(command)))
    
    # Generar una respuesta utilizando la API de OpenAI
    response = openai.Completion.create(
        engine="davinci",
        prompt=command,
        max_tokens=50
    ).choices[0].text
    
    # Reproducir la respuesta utilizando el motor de síntesis de voz
    engine.say(response)
    engine.runAndWait()

# Utilizar Speech Recognition para transcribir los comandos de voz del usuario
r = sr.Recognizer()

# Ajustar el umbral de silencio manualmente
r.energy_threshold = 4000

with sr.Microphone(device_index=1) as source:  # Configurar el índice del dispositivo de audio según sea necesario
    print("Di algo...")
    
    # Escuchar el audio del micrófono
    audio = r.listen(source)

# Procesar el comando de voz del usuario
try:
    command = r.recognize_google(audio, language=language)
    process_command(command)
except sr.UnknownValueError:
    engine.say("Lo siento, no pude entender lo que dijiste.")
    engine.runAndWait()
except sr.RequestError:
    engine.say("Lo siento, hay un problema con mi servicio de reconocimiento de voz.")
    engine.runAndWait()
