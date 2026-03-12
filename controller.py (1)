import time
import threading
from flask import Flask
from supabase import create_client
import requests
import os

# 1. Kleiner Web-Server für Render (damit es "Live" bleibt)
app = Flask(__name__)

@app.route('/')
def health_check():
    return "Controller is alive!", 200

def run_flask():
    # Render vergibt den Port automatisch über die Variable PORT
    port = int(os.environ.get("PORT", 10000))
    app.run(host='0.0.0.0', port=port)

# 2. Deine Supabase-Logik
URL = "https://rhvswulsykwvumrwsxyw.supabase.co"
KEY = "Sb_publishable_mF35FI-CHx0bpVD286xIeQ_PkEJ6bhQ"
supabase = create_client(URL, KEY)

def controller_loop():
    print("--- AutoHar Controller gestartet ---")
    while True:
        try:
            # Check die Datenbank
            response = supabase.table("Doan-author").select("*").execute()
            if response.data:
                for entry in response.data:
                    hook = entry.get('webhook_url')
                    name = entry.get('directory_name')
                    row_id = entry.get('id')
                    
                    # Sende Nachricht an Discord
                    requests.post(hook, json={"content": f"🚀 AutoHar bereit: {name}"})
                    
                    # Lösche aus Datenbank
                    supabase.table("Doan-author").delete().eq("id", row_id).execute()
                    print(f"✅ Webhook gesendet an: {name}")
        except Exception as e:
            print(f"Fehler in der Loop: {e}")
        
        time.sleep(15) # Alle 15 Sekunden prüfen

if __name__ == "__main__":
    # Startet den Web-Server in einem eigenen Thread
    threading.Thread(target=run_flask, daemon=True).start()
    # Startet die Datenbank-Schleife
    controller_loop()
