
from telegram import Update
from telegram.ext import ApplicationBuilder, ContextTypes, MessageHandler, filters
import re
import os

CHANNEL_USERNAME = '@Film_seryal_best'
CHANNEL_LINK = 't.me/Film_seryal_best'

def remove_links(text):
    return re.sub(r'http[s]?://\S+|www\.\S+', '', text).strip()

async def handle_post(update: Update, context: ContextTypes.DEFAULT_TYPE):
    if update.channel_post:
        message = update.channel_post
        text = message.text or message.caption

        if text:
            clean_text = remove_links(text)
            final_text = f"{clean_text}\n\n{CHANNEL_LINK}"

            try:
                if message.text:
                    await context.bot.edit_message_text(
                        chat_id=message.chat_id,
                        message_id=message.message_id,
                        text=final_text
                    )
                elif message.caption:
                    await context.bot.edit_message_caption(
                        chat_id=message.chat_id,
                        message_id=message.message_id,
                        caption=final_text
                    )
            except Exception as e:
                print(f"Error editing message: {e}")

if __name__ == '__main__':
    TOKEN = os.getenv("BOT_TOKEN")
    app = ApplicationBuilder().token(TOKEN).build()
    app.add_handler(MessageHandler(filters.UpdateType.CHANNEL_POST, handle_post))
    print("Bot is running...")
    app.run_polling()
