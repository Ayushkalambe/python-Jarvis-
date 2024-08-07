# python-Jarvis-
This is my first python project...
import speech_recognition as sr
import pyttsx3
import webbrowser
import musicfile # create your own file which contains songs and name them musicfile
from gtts import gTTS
import pygame
import os
from openai import OpenAI

recognizer = sr.Recognizer()

engine = pyttsx3.init() 

#def speak_old(text):
  #  engine.say(text)
   # engine.runAndWait()

def speak(text):
    tts = gTTS(text)
    tts.save('temp.mp3') 

    # Initialize Pygame mixer
    pygame.mixer.init()

    # Load the MP3 file
    pygame.mixer.music.load('temp.mp3')

    # Play the MP3 file
    pygame.mixer.music.play()

    # Keep the program running until the music stops playing
    while pygame.mixer.music.get_busy():
        pygame.time.Clock().tick(10)
    
    pygame.mixer.music.unload()
    os.remove("temp.mp3") 

def aiProcess(c):
    client = OpenAI(api_key=" ") #enter your API key here

    completion = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[
        {"role": "system", "content": "You are a virtual assistant named jarvis skilled in general tasks like Alexa and Google Cloud. Give short responses please"},
        {"role": "user", "content": c}
    ]
    )

    return completion.choices[0].message.content


def processcommand(c):
     
     if "open" in c.lower():
          opens = c.lower().split(" ")[1]
          webbrowser.open(f"https://{opens}.com")
        
        # if you want to open webpage of different domain rather than .com so you have to add this functionality...
     elif "open amazon" in c.lower():
          webbrowser.open("https://amazon.in")
    
     elif c.lower().startswith("play"):
         song = c.lower().split(" ")[1]
         link = musicfile.music[song]
         webbrowser.open(link)

     else:
         output = aiProcess(command)
         speak(output) 


        
if __name__ == "__main__":
    speak("Initializing Jarvis....")
    while True:
        r = sr.Recognizer()
        print("recognizing...")
        try:
            with sr.Microphone() as source:
                print("Listening...")
                audio = r.listen(source, timeout=2, phrase_time_limit=1)
            word = r.recognize_google(audio)
            if(word.lower() == "jarvis"):
                speak("Yes sir..")
                # Listen for command
                with sr.Microphone() as source:
                    print("Jarvis Active...")
                    audio = r.listen(source)
                    command = r.recognize_google(audio)

                    processcommand(command)


        except Exception as e:
            print("Error; {0}".format(e))


