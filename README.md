# telegram-bot-24h
Automation bot with 500 hours per month
# Import các thư viện cần thiết
from telegram import Update
from telegram.ext import ApplicationBuilder, MessageHandler, filters
import logging
import re  # Thêm thư viện regex

# Cấu hình logging để dễ debug
logging.basicConfig(format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', level=logging.INFO)
logger = logging.getLogger(__name__)

# Token của bot (thay bằng token bạn nhận từ BotFather)
TOKEN = "7429494966:AAEFzEYdn03Wy7gdgDW_ejSdRTe9OlyuAZI"

# Danh sách thông tin ngân hàng
bank_data = [
    # [Danh sách bank_data giữ nguyên như code của bạn]
    {
        "bank_name": "VIETCOMBANK",
        "account_number": "1054359067",
        "account_name": "NGO HONG LAM",
        "gateway": "Fast1"
    },
    {
        "bank_name": "VIETCOMBANK",
        "account_number": "0031000348150",
        "account_name": "PHAM NGOC THUONG",
        "gateway": "ApplePay"
    },
    {
        "bank_name": "PVcombank",
        "account_number": "106003021562",
        "account_name": "TRAN THE VINH",
        "gateway": "J-Pay"
    },
    {
        "bank_name": "BIDV",
        "account_number": "8864277826",
        "account_name": "TRAN DINH LINH",
        "gateway": "J-Pay"
    },
    {
        "bank_name": "TPBank",
        "account_number": "10001104155",
        "account_name": "TRAN DINH LINH",
        "gateway": "J-Pay"
    },
    {
        "bank_name": "VIETCOMBANK",
        "account_number": "1055710758",
        "account_name": "DINH LE HOANG ANH",
        "gateway": "Fast1"
    },
    {
        "bank_name": "BIDV",
        "account_number": "8864622902",
        "account_name": "NGUYEN THI MAI",
        "gateway": "F-Pay"
    },
    {
        "bank_name": "VIETINBANK",
        "account_number": "100883552144",
        "account_name": "NGUYEN MANH HUNG",
        "gateway": "F-Pay"
    },
    {
        "bank_name": "ACB",
        "account_number": "44383747",
        "account_name": "LE THI HONG HAI",
        "gateway": "HN-Pay | HN-QR"
    },
    {
        "bank_name": "BIDV",
        "account_number": "8864687571",
        "account_name": "LE THI HONG HAI",
        "gateway": "HN-Pay | HN-QR"
    },
    {
        "bank_name": "TECHCOMBANK",
        "account_number": "3387341432",
        "account_name": "LE THI HONG HAI",
        "gateway": "HN-Pay | HN-QR"
    },
    {
        "bank_name": "VIETCOMBANK",
        "account_number": "1051860258",
        "account_name": "LE THI HONG HAI",
        "gateway": "HN-Pay | HN-QR"
    },
    {
        "bank_name": "VIETCOMBANK",
        "account_number": "1012708617",
        "account_name": "PHAM THI HONG",
        "gateway": "DR-Pay"
    },
    {
        "bank_name": "MBBank",
        "account_number": "5050100418888",
        "account_name": "PHAM THI HONG",
        "gateway": "DR-Pay"
    },
    {
        "bank_name": "MSB",
        "account_number": "80000845440",
        "account_name": "THAI TRUONG HUY",
        "gateway": "MBM-Pay"
    },
    {
        "bank_name": "VIETCOMBANK",
        "account_number": "0201000638299",
        "account_name": "THAI TRUONG HUY",
        "gateway": "MBM-Pay"
    },
    {
        "bank_name": "VIETCOMBANK",
        "account_number": "1025407299",
        "account_name": "VU NGOC TIEN",
        "gateway": "IPay không phải L-Pay nhé cả nhà"
    },
    {
        "bank_name": "PVcombank",
        "account_number": "101002784856",
        "account_name": "HA VIET DUC",
        "gateway": "J-Pay"
    },
    {
        "bank_name": "VPBank",
        "account_number": "30230082000",
        "account_name": "LE BA TRUNG",
        "gateway": "J-Pay"
    },
    {
        "bank_name": "VIETCOMBANK",
        "account_number": "1054827369",
        "account_name": "BACH THAI QUOC",
        "gateway": "L-Pay"
    },
    {
        "bank_name": "OCB",
        "account_number": "6000052",
        "account_name": "NGO TRUONG DUY",
        "gateway": "L-Pay"
    },
    {
        "bank_name": "BIDV",
        "account_number": "8884574821",
        "account_name": "TONG THI THANH PHUONG",
        "gateway": "L-Pay"
    },
    {
        "bank_name": "BIDV",
        "account_number": "6411030321",
        "account_name": "HUYNH VAN HOAI",
        "gateway": "F-Pay"
    },
    {
        "bank_name": "VIETINBANK",
        "account_number": "108883779198",
        "account_name": "DO XUAN HUNG",
        "gateway": "J-Pay"
    },
    {
        "bank_name": "TECHCOMBANK",
        "account_number": "19039241406010",
        "account_name": "NGUYEN NHAT QUYNH",
        "gateway": "Sky-Pay"
    },
    {
        "bank_name": "VIETCOMBANK",
        "account_number": "1053973701",
        "account_name": "LE BA TRUNG",
        "gateway": "J-Pay"
    },
    {
        "bank_name": "BIDV",
        "account_number": "8864815606",
        "account_name": "PHAM NGOC THUONG",
        "gateway": "ApplePay"
    },
    {
        "bank_name": "MBBank",
        "account_number": "978393979",
        "account_name": "NGUYEN THI NHU QUYNH",
        "gateway": "MBM-Pay"
    },
    {
         "bank_name": "BDIV",
        "account_number": "8824721655",
        "account_name": "VU THANH TUNG",
        "gateway": "F-Pay"
    },
    {
        "bank_name": "VIETINBANK",
        "account_number": "108885665222",
        "account_name": "VU THANH TUNG",
        "gateway": "F-Pay"
    },
    {
        "bank_name": "VIETINBANK",
        "account_number": "115003002806",
        "account_name": "CT TNHH CUNG UNG NLQT",
        "gateway": "MBM-Pay"
    },
    {
        "bank_name": "VIETINBANK",
        "account_number": "106883656620",
        "account_name": "BUI VAN DON",
        "gateway": "Fast1"
    },
    {
        "bank_name": "VIETCOMBANK",
        "account_number": "1055808148",
        "account_name": "LE HOANG HAO",
        "gateway": "L-Momo"
    },
    {
        "bank_name": "BIDV",
        "account_number": "8803530954",
        "account_name": "HO VAN NHO",
        "gateway": "J-Pay"
    },
    {
        "bank_name": "VPBANK",
        "account_number": "0327854774",
        "account_name": "NGUYEN NHAT QUYNH",
        "gateway": "F-Pay"
    },
    {
        "bank_name": "VPBANK",
        "account_number": "0342144751",
        "account_name": "DU THI PHUONG THAO",
        "gateway": "F-Pay"
    },
    {
        "bank_name": "VPBANK",
        "account_number": "0989783608",
        "account_name": "NGUYEN HONG LINH",
        "gateway": "F-Pay"
    },
    {
        "bank_name": "VIETCOMBANK",
        "account_number": "1052235937",
        "account_name": "TRAN QUANG SON",
        "gateway": "ApplePay"
    },
    {
        "bank_name": "VPBANK",
        "account_number": "398947045",
        "account_name": "TRAN DINH LINH",
        "gateway": "J-Zalo"
    },
    {
        "bank_name": "OCB",
        "account_number": "8000088",
        "account_name": "VO THANH BAC",
        "gateway": "L-Pay"
    },
    {
        "bank_name": "MBBank",
        "account_number": "90349038404",
        "account_name": "NGO HONG LAM",
        "gateway": "Fast"
    },
    {
        "bank_name": "VIETINBANK",
        "account_number": "101883575990",
        "account_name": "HUYNH VAN HOAI",
        "gateway": "F-Pay"
    },
    {
        "bank_name": "VPBANK",
        "account_number": "0966765055",
        "account_name": "TRUONG THI KIM DUNG",
        "gateway": "F-Pay"
    },
    {
        "bank_name": "MBBank",
        "account_number": "9135188964349",
        "account_name": "NGUYEN MINH TRI",
        "gateway": "L-Pay"
    },
    {
        "bank_name": "OCB",
        "account_number": "0902658815",
        "account_name": "DUONG THI CHINH",
        "gateway": "IPay"
    },
    {
        "bank_name": "MSB",
        "account_number": "80000807367",
        "account_name": "DOAN NGOC HIEU",
        "gateway": "J-Pay"
    },
    # ... (các mục khác giữ nguyên)
    {
        "bank_name": "TECHCOMBANK",
        "account_number": "190123456789",
        "account_name": "NGUYEN VAN A",
        "gateway": "MT-Pay"
    }
]

# Hàm xử lý tin nhắn
async def handle_message(update: Update, context):
    # Lấy nội dung tin nhắn người dùng gửi và chuyển về chữ thường
    text = update.message.text.lower()  # Chuyển về chữ thường
    original_text = update.message.text  # Giữ nguyên bản gốc để reply
    logger.info(f"Received message: {text}")  # Log tin nhắn để debug

    # Tạo từ điển ánh xạ mã với nhóm
    mapping = {
        "ff": "F-Pay",
        "nn": "Sky-Pay",
        "gg": "Sky-Pay",
        "kk": "Sky-Pay",
        "aa": "Sky-Pay",
        "bb": "Sky-Pay",
        "cc": "Sky-Pay",
        "dd": "Sky-Pay",
        "ee": "Sky-Pay",
        "gg": "Sky-Pay",
        "hh": "Sky-Pay",
        "ii": "Sky-Pay",
        "ll": "Sky-Pay",
        "mm": "Sky-Pay",
        "nn": "Sky-Pay",
        "nn": "Sky-Pay",
        "ss": "Sky-Pay",
        "jj": "Sky-Pay",
        "rr": "Sky-Pay",
        "qq": "Sky-Pay",
        "ww": "Sky-Pay",
        "ee": "Sky-Pay",
        "tt": "Sky-Pay",
        "yy": "Sky-Pay",
        "uu": "Sky-Pay",
        "dy": "L-Pay",
        "muasam": "HiPay",
        "ckf": "Fast1",
        "a00": "MBM Pay",
        "sp": "DirePay",
        "ne": "DirePay",
        "fa": "L-Pay",
        "gd": "L-Pay",
        "chf": "Fast1",
        "mua hàng": "MT-Pay",
        "thanh toán": "MT-Pay",
        "ubs": "L-Momo"
    }

    # Mặc định là chưa tìm thấy
    reply = "❌ Không tìm thấy thông tin. Vui lòng nhập mã hợp lệ (VD: FF), tên ngân hàng hoặc họ tên"

    # Kiểm tra xem text có trong mapping không
    if text in mapping:
        reply = f"✅ {original_text} thuộc {mapping[text]}"
    else:
        # Nếu là định dạng "2 chữ cái + 5 số" => IPay
        if re.fullmatch(r'[a-zA-Z]{2}\d{5}', text):
            reply = f"✅ {original_text} thuộc IPay (IPay không phải L-Pay nhé cả nhà iu)"
        # Nếu là định dạng xen kẽ chữ-số => HN-Pay
        elif re.fullmatch(r'([a-zA-Z]\d){3}', text):
            reply = f"✅ {original_text} thuộc HN-Pay"
        # Nếu là 4 ký tự số => I-Pay (thêm điều kiện mới)
        elif re.fullmatch(r'\d{4}', text):
            reply = f"✅ {original_text} thuộc I-Pay"
        # Nếu là 4 ký tự bất kỳ gồm cả chữ và số => J-Pay
        elif re.fullmatch(r'[a-zA-Z0-9]{4}', text):
            reply = f"✅ {original_text} thuộc J-Pay"
        # Nếu là 6 ký tự bao gồm cả chữ và số (không phân biệt thứ tự) => ApplePay
        elif re.fullmatch(r'[a-zA-Z0-9]{6}', text) and not re.fullmatch(r'([a-zA-Z]\d){3}', text):
            reply = f"✅ {original_text} thuộc ApplePay"
        elif re.fullmatch(r'\d{5}', text):
            reply = f"✅ {original_text} thuộc IPay (IPay không phải L-Pay nhé cả nhà iu)"
        else:
            # Tìm kiếm theo số tài khoản, tên ngân hàng hoặc họ tên
            matches = []
            for bank in bank_data: 
                # Tìm theo số tài khoản
                if text == bank["account_number"].lower():
                    matches.append(
                        f"Tên ngân hàng: {bank['bank_name']}\n"
                        f"Số tài khoản: {bank['account_number']}\n"
                        f"Họ tên: {bank['account_name']}\n"
                        f"Cổng: {bank['gateway']}"
                    )
                    break  # Thoát vòng lặp sau khi tìm thấy số tài khoản
                # Tìm theo tên ngân hàng
                elif text == bank["bank_name"].lower():
                    matches.append(
                        f"Tên ngân hàng: {bank['bank_name']}\n"
                        f"Số tài khoản: {bank['account_number']}\n"
                        f"Họ tên: {bank['account_name']}\n"
                        f"Cổng: {bank['gateway']}"
                    )
                # Tìm theo họ tên
                elif text == bank["account_name"].lower():
                    # Tìm tất cả tài khoản có cùng họ tên
                    matches = [
                        f"Tên ngân hàng: {b['bank_name']}\n"
                        f"Số tài khoản: {b['account_number']}\n"
                        f"Họ tên: {b['account_name']}\n"
                        f"Cổng: {b['gateway']}"
                        for b in bank_data if b["account_name"].lower() == text
                    ]
                    break  # Thoát vòng lặp sau khi tìm thấy họ tên

            if matches:
                if len(matches) == 1:
                    reply = f"💩 Cố lên cố lên sắp tới òi sắp tới òi:\n{matches[0]}"
                else:
                    reply = "💩 Cố lên cố lên sắp tới òi sắp tới òi:\n\n" + "\n\n".join([f"[{i+1}]\n{match}" for i, match in enumerate(matches)])
            else:
                reply = "❌ Sai rồi ní ơi, của ní hết 50K!"

    # Gửi phản hồi cho người dùng
    await update.message.reply_text(reply)

# Tạo ứng dụng bot và thêm handler
app = ApplicationBuilder().token(TOKEN).build()
app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, handle_message))

# Chạy bot
print("Bot is running...")
app.run_polling()
