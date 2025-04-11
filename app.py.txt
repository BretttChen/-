# 匯入需要的工具
from flask import Flask, request, jsonify
from flask_cors import CORS
import logging

# 設定基本的日誌，方便在終端機看訊息
logging.basicConfig(level=logging.INFO)

# 建立 Flask 應用程式
app = Flask(__name__)
# 啟用 CORS，允許來自前端的請求 (開發時用)
CORS(app)

# 設定一個路由：當有人用 POST 方法訪問 /api/contact 時，執行這個函數
@app.route('/api/contact', methods=['POST'])
def handle_contact_form():
    """這個函數負責處理聯絡表單的提交"""
    logging.info("後端：收到來自 /api/contact 的 POST 請求")

    # 檢查收到的資料是不是 JSON 格式
    if not request.is_json:
        logging.warning("後端：收到的請求不是 JSON 格式")
        return jsonify({"status": "error", "message": "請求格式錯誤，後端需要 JSON"}), 400

    try:
        # 獲取前端發送過來的 JSON 資料，轉換成 Python 字典
        data = request.get_json()
        logging.info(f"後端：收到的資料內容: {data}")

        # --- 這裡就是後端真正處理資料的地方 ---
        # 1. 可以做更嚴謹的資料驗證
        name = data.get('name')
        email = data.get('email')
        message = data.get('message')
        if not name or not email or not message:
            logging.warning("後端：缺少必要欄位 (姓名、Email 或訊息)")
            # 回傳錯誤訊息給前端
            return jsonify({"status": "error", "message": "請確認姓名、Email 和訊息都已填寫。"}), 400

        # 2. 把資料存到資料庫 (現在先省略，只印出來)
        print("----- 收到新的聯絡表單 -----")
        print(f"  姓名: {name}")
        print(f"  Email: {email}")
        print(f"  電話: {data.get('phone', '(未提供)')}")
        print(f"  主旨: {data.get('subject', '(未提供)')}")
        print(f"  訊息: {message}")
        print("-----------------------------")

        # 3. 寄 Email 通知民宿主人 (現在也省略)

        # --- 資料處理完成 ---

        # 回傳一個成功的 JSON 回應給前端
        logging.info("後端：資料處理完畢，回傳成功訊息")
        return jsonify({"status": "success", "message": "後端：訊息已成功收到，我們會盡快回覆您！"})

    except Exception as e:
        # 如果處理過程中發生任何預期外的錯誤
        logging.error(f"後端：處理表單時發生錯誤: {e}")
        # 回傳伺服器錯誤訊息給前端
        return jsonify({"status": "error", "message": "後端伺服器處理時發生錯誤，請稍後再試。"}), 500

# 這段是讓你可以直接用 python app.py 來啟動伺服器
if __name__ == '__main__':
    # 啟動 Flask 開發伺服器
    # host='0.0.0.0' 讓同區域網路的裝置也能連
    # port=5000 指定埠號
    # debug=True 開啟除錯模式，修改程式碼會自動重啟，且會顯示詳細錯誤
    app.run(host='0.0.0.0', port=5000, debug=True)