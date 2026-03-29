# -
Нефритовый дворец 
import logging
import os
from aiogram import Bot, Dispatcher, types
from aiogram.utils import executor
import yt_dlp

API_TOKEN = os.getenv("TOKEN")

logging.basicConfig(level=logging.INFO)

bot = Bot(token=API_TOKEN)
dp = Dispatcher(bot)

@dp.message_handler(commands=['start'])
async def start(msg: types.Message):
    await msg.answer("Отправь ссылку 🎬")

@dp.message_handler()
async def download(msg: types.Message):
    url = msg.text
    await msg.answer("⏳ Скачиваю...")

    try:
        ydl_opts = {'format': 'best', 'outtmpl': 'file.%(ext)s'}

        with yt_dlp.YoutubeDL(ydl_opts) as ydl:
            info = ydl.extract_info(url, download=True)
            file = ydl.prepare_filename(info)

        with open(file, 'rb') as f:
            await msg.answer_video(f)

        os.remove(file)

    except Exception as e:
        await msg.answer(f"Ошибка: {e}")

if __name__ == "__main__":
    executor.start_polling(dp)
