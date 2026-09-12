"""
SIMF — бот для приёма фото от подписчиков.

Что делает:
1. Принимает фото от любого пользователя в личных сообщениях.
2. Пересылает фото вам (администратору) вместе с именем/ником автора
   и подписью, если пользователь её добавил.
3. Отвечает пользователю подтверждением, что фото получено.

Установка:
    pip install python-telegram-bot --upgrade

Запуск:
    python simf_bot.py
"""

import os
import logging
from telegram import Update
from telegram.ext import (
    ApplicationBuilder,
    CommandHandler,
    MessageHandler,
    ContextTypes,
    filters,
)

# ==== НАСТРОЙКИ ====
# Значения берутся из переменных окружения (задаются в Railway → Variables).
# Для локального теста можно временно вписать значения напрямую вместо
# os.environ[...], но не заливайте токен в GitHub в открытом виде.
BOT_TOKEN = os.environ["8583494554:AAHky1NkHhNK0nzHcMRvHQcPQNMZf0dA6Xw"]
ADMIN_CHAT_ID = int(os.environ["657429808"])
# ====================

logging.basicConfig(
    format="%(asctime)s - %(name)s - %(levelname)s - %(message)s",
    level=logging.INFO,
)
logger = logging.getLogger(__name__)


async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text(
        "Привет! Это бот канала SIMF 📸\n\n"
        "Просто пришли сюда фото Симферополя — при желании добавь подпись "
        "(где и когда снято). Мы отберём лучшие кадры и опубликуем их в канале."
    )


async def handle_photo(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user = update.effective_user
    photo = update.message.photo[-1]  # берём фото в максимальном качестве
    caption = update.message.caption or "(без подписи)"

    author = f"@{user.username}" if user.username else user.full_name

    # Пересылаем фото администратору с данными автора
    info_text = (
        f"📸 Новое фото на модерацию\n\n"
        f"От: {author} (id: {user.id})\n"
        f"Подпись: {caption}"
    )

    await context.bot.send_photo(
        chat_id=ADMIN_CHAT_ID,
        photo=photo.file_id,
        caption=info_text,
    )

    # Подтверждение отправителю
    await update.message.reply_text(
        "Спасибо! Фото получено и передано на модерацию 🙌"
    )


async def handle_other(update: Update, context: ContextTypes.DEFAULT_TYPE):
    # Если прислали не фото, а текст/документ и т.п.
    await update.message.reply_text(
        "Пришлите, пожалуйста, именно фото (как изображение, не файлом), "
        "чтобы оно попало на модерацию."
    )


def main():
    app = ApplicationBuilder().token(BOT_TOKEN).build()

    app.add_handler(CommandHandler("start", start))
    app.add_handler(MessageHandler(filters.PHOTO, handle_photo))
    app.add_handler(MessageHandler(~filters.PHOTO & ~filters.COMMAND, handle_other))

    logger.info("Бот запущен...")
    app.run_polling()


if __name__ == "__main__":
    main()
