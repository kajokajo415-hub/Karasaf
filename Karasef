import os
import logging
from telegram import Update, InlineKeyboardButton, InlineKeyboardMarkup
from telegram.ext import ApplicationBuilder, CommandHandler, CallbackQueryHandler, ContextTypes

logging.basicConfig(
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    level=logging.INFO
)
logger = logging.getLogger(__name__)

user_balances = {}

def get_balance(user_id):
    return user_balances.get(user_id, 0)

def add_balance(user_id, amount):
    user_balances[user_id] = get_balance(user_id) + amount

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user = update.effective_user
    balance = get_balance(user.id)
    keyboard = [
        [InlineKeyboardButton("💳 شحن الرصيد", callback_data="charge_menu")],
        [InlineKeyboardButton("💰 رصيدي الحالي", callback_data="my_balance")],
    ]
    reply_markup = InlineKeyboardMarkup(keyboard)
    await update.message.reply_text(
        f"👋 أهلاً {user.first_name}!\n\n"
        f"💰 رصيدك الحالي: *{balance}$*\n\n"
        "اختر من القائمة:",
        parse_mode="Markdown",
        reply_markup=reply_markup
    )

async def button_handler(update: Update, context: ContextTypes.DEFAULT_TYPE):
    query = update.callback_query
    await query.answer()
    user = query.from_user
    if query.data == "charge_menu":
        keyboard = [
            [InlineKeyboardButton("5$", callback_data="charge_5"),
             InlineKeyboardButton("10$", callback_data="charge_10")],
            [InlineKeyboardButton("20$", callback_data="charge_20"),
             InlineKeyboardButton("50$", callback_data="charge_50")],
            [InlineKeyboardButton("🔙 رجوع", callback_data="back_main")],
        ]
        reply_markup = InlineKeyboardMarkup(keyboard)
        await query.edit_message_text(
            "💳 *اختر مبلغ الشحن:*\n\nاضغط على المبلغ اللي بدك تشحنه:",
            parse_mode="Markdown",
            reply_markup=reply_markup
        )
    elif query.data.startswith("charge_"):
        amount = int(query.data.split("_")[1])
        add_balance(user.id, amount)
        new_balance = get_balance(user.id)
        keyboard = [
            [InlineKeyboardButton("💳 شحن مرة ثانية", callback_data="charge_menu")],
            [InlineKeyboardButton("🏠 القائمة الرئيسية", callback_data="back_main")],
        ]
        reply_markup = InlineKeyboardMarkup(keyboard)
        await query.edit_message_text(
            f"✅ *تم شحن رصيدك بنجاح!*\n\nالمبلغ المشحون: *{amount}$*\n💰 رصيدك الجديد: *{new_balance}$*",
            parse_mode="Markdown",
            reply_markup=reply_markup
        )
    elif query.data == "my_balance":
        balance = get_balance(user.id)
        keyboard = [
            [InlineKeyboardButton("💳 شحن الرصيد", callback_data="charge_menu")],
            [InlineKeyboardButton("🏠 القائمة الرئيسية", callback_data="back_main")],
        ]
        reply_markup = InlineKeyboardMarkup(keyboard)
        await query.edit_message_text(
            f"💰 *رصيدك الحالي:*\n\n*{balance}$*",
            parse_mode="Markdown",
            reply_markup=reply_markup
        )
    elif query.data == "back_main":
        balance = get_balance(user.id)
        keyboard = [
            [InlineKeyboardButton("💳 شحن الرصيد", callback_data="charge_menu")],
            [InlineKeyboardButton("💰 رصيدي الحالي", callback_data="my_balance")],
        ]
        reply_markup = InlineKeyboardMarkup(keyboard)
        await query.edit_message_text(
            f"🏠 *القائمة الرئيسية*\n\n💰 رصيدك الحالي: *{balance}$*\n\nاختر من القائمة:",
            parse_mode="Markdown",
            reply_markup=reply_markup
        )

def main():
    token = os.environ.get("TELEGRAM_BOT_TOKEN")
    if not token:
        logger.error("TELEGRAM_BOT_TOKEN غير موجود!")
        return
    app = ApplicationBuilder().token(token).build()
    app.add_handler(CommandHandler("start", start))
    app.add_handler(CallbackQueryHandler(button_handler))
    logger.info("البوت شغال...")
    app.run_polling()

if __name__ == "__main__":
    main()
