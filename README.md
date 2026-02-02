from telegram import Update, InlineKeyboardButton, InlineKeyboardMarkup
from telegram.ext import Application, CommandHandler, CallbackQueryHandler, ContextTypes

TOKEN = "8421434971:AAFXgBY6kmyCFgcsqiy7p8ZGGbjtjnSbWsM"
VIP_LINK = "https://t.me/+cxEatxTIRztkYmU9"

CHANNELS = [
    ("MR GURU OFFICIAL", "@mrguruo"),
    ("MRS KEEL OFFICIAL", "@mrskeel")
]

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    buttons = [
        [InlineKeyboardButton(name, url=f"https://t.me/{username[1:]}")]
        for name, username in CHANNELS
    ]
    buttons.append([InlineKeyboardButton("✅ CHECK JOIN", callback_data="check")])

    await update.message.reply_text(
        "🔐 *VIP ACCESS SYSTEM*\n\n"
        "👇 Pehle niche diye gaye channels join karo\n"
        "phir *CHECK JOIN* par click karo",
        reply_markup=InlineKeyboardMarkup(buttons),
        parse_mode="Markdown"
    )

async def check_join(update: Update, context: ContextTypes.DEFAULT_TYPE):
    query = update.callback_query
    await query.answer()

    user_id = query.from_user.id

    for name, username in CHANNELS:
        try:
            member = await context.bot.get_chat_member(username, user_id)
            if member.status not in ["member", "administrator", "creator"]:
                await query.message.reply_text(
                    f"❌ Pehle *{name}* join karo",
                    parse_mode="Markdown"
                )
                return
        except:
            await query.message.reply_text(
                f"⚠️ Bot ko *{name}* channel me ADMIN banao",
                parse_mode="Markdown"
            )
            return

    await query.message.reply_text(
        f"🎉 *VIP ACCESS UNLOCKED!*\n\n👉 {VIP_LINK}",
        parse_mode="Markdown"
    )

def main():
    app = (
        Application.builder()
        .token(TOKEN)
        .connect_timeout(30)
        .read_timeout(30)
        .build()
    )

    app.add_handler(CommandHandler("start", start))
    app.add_handler(CallbackQueryHandler(check_join, pattern="^check$"))

    app.run_polling()

if __name__ == "__main__":
    main()
