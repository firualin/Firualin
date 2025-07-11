import os
import logging
import time
import threading
import schedule
from telegram import Bot
from telegram.ext import CommandHandler, Updater

# === CONFIG ===
TOKEN = os.getenv("TELEGRAM_TOKEN")  # în Railway pui tokenul aici
CHAT_ID = "alin271979"  # numele tău Telegram
bot = Bot(token=TOKEN)

# === ALERTĂ SIMPLĂ ===
def send_alert(message):
    try:
        bot.send_message(chat_id=f"@{CHAT_ID}", text=message)
    except Exception as e:
        print(f"Eroare la trimitere alertă: {e}")

# === MESAJ TEST ===
def start(update, context):
    context.bot.send_message(chat_id=update.effective_chat.id, text="✅ Bot activ! Voi trimite alerte automate.")

# === ANALIZĂ ZILNICĂ PROGRAMATĂ ===
def daily_update():
    mesaj = "📊 Analiză zilnică:\n• TON = 2.91\n• TRX = 0.29\n• NEAR = 2.36\n• INJ = 11.00\n• FLOKI = 0.00001\n🔔 Vezi și pozițiile active."
    send_alert(mesaj)

# === Alerte de preț fictive (poți lega de API real) ===
def check_prices():
    # Aici simulezi condiții (în real poți lega de CoinGecko/CoinMarketCap API)
    if True:
        send_alert("🚨 TON a scăzut sub 2.85 USD — oportunitate de BUY!")
    if True:
        send_alert("🚀 OMNI a trecut peste 5.00 USD — semnal de breakout!")

# === Scheduler ===
def run_schedule():
    schedule.every().day.at("09:00").do(daily_update)
    while True:
        schedule.run_pending()
        time.sleep(30)

# === MAIN ===
def main():
    logging.basicConfig(level=logging.INFO)
    updater = Updater(token=TOKEN, use_context=True)
    dispatcher = updater.dispatcher
    dispatcher.add_handler(CommandHandler('start', start))

    # Pornim thread separat pentru programare
    threading.Thread(target=run_schedule).start()

    # Poți adăuga altă funcție care rulează la 5min
    schedule.every(5).minutes.do(check_prices)

    updater.start_polling()
    updater.idle()

if __name__ == '__main__':
    main()
