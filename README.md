# AI-Voice-Assistant
import speech_recognition as sr
import nltk
from textblob import TextBlob
import pyttsx3
import openai

# Inicializar el motor de síntesis de voz
engine = pyttsx3.init()

# Configurar opciones de voz
voices = engine.getProperty('voices')
engine.setProperty('voice', voices[0].id)
engine.setProperty('rate', 150)

# Configurar la API Key de OpenAI
openai.api_key = "OPEN-AI-KEY"

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
with sr.Microphone(device_index=1) as source:  # Configurar el índice del dispositivo de audio según sea necesario
    print("Di algo...")
    
    # Ajustar automáticamente el umbral de silencio
    r.adjust_for_ambient_noise(source)
    
    # Escuchar el audio del micrófono
    audio = r.listen(source)

# Procesar el comando de voz del usuario
try:
    command = r.recognize_google(audio, language='es-ES')
    process_command(command)
except sr.UnknownValueError:
    engine.say("Lo siento, no pude entender lo que dijiste.")
    engine.runAndWait()
except sr.RequestError:
    engine.say("Lo siento, hay un problema con mi servicio de reconocimiento de voz.")
    engine.runAndWait()
