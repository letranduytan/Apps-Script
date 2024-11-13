# Expense Management with TelegramBot
</p>
<p align="center">
<a href="https://twitter.com/12dtan" target="_blank"><img src="https://img.shields.io/twitter/follow/12dtan.svg?style=social&label=Follow"></a>
<a href="https://fb.com/duytan.hh" target="_blank"><img src="https://img.shields.io/badge/Facebook%20-%20%230866FF"></a>
<a href="https://t.me/duytan2003" target="_blank"><img src="https://img.shields.io/badge/Telegram%20-%20%2333CCFF"></a>
<a href="https://www.linkedin.com/in/l%C3%AA-tr%E1%BA%A7n-duy-t%C3%A2n-81112a23a/" target="_blank"><img src="https://img.shields.io/badge/Linkedin%20-%20%2300CCFF"></a>
<a href="https://instagram/duytan.hh" target="_blank"><img src="https://img.shields.io/badge/Instagram%20-%20%23FF9900"></a>
</p>

## Connect telegram with google sheets
### Cài đặt Webhook
> Trước khi bắt đầu, hãy dán URL của ứng dụng web và chạy hàm `setWebhook`.

**Phương thức:** `https://api.telegram.org/bot<token>/METHOD_NAME`

```javascript
var webapp = 'điền URL web app của bạn vào đây' 
var token = 'điền token bot của bạn vào đây'; 
var url = 'https://api.telegram.org/bot' + token;
var chatid = 'điền chat id vào đây';
```

- Để lấy **chat ID** cá nhân, hãy sử dụng bot @userinfobot. 
- Để lấy **chat ID** của nhóm, làm theo các bước sau:
  1. Thêm bot vào một nhóm mới.
  2. Gửi một tin nhắn trong nhóm có nhắc đến bot.
  3. Dán URL sau vào trình duyệt (với token bot của bạn): `https://api.telegram.org/bot<token>/getUpdates`.
  4. Lấy **chat ID** từ kết quả.

## Kết nối Sheets & Telegram

#### Đặt Webhook
```javascript
function setWebhook(){
      var response = UrlFetchApp.fetch(url + '/setWebhook?url=' + webapp) 
      Logger.log(response)
}
```

#### Gửi tin nhắn từ bot đến bạn (hoặc nhóm)
```javascript
function sendMessage(body){
  var response = UrlFetchApp.fetch(url + '/sendMessage?chat_id=' + chatid + '&text=' + encodeURIComponent(body) + '&parse_mode=HTML')
}
```

## Quản lý Google Sheets từ Telegram

#### Mã chức năng `doPost`
- **Giải thích:** Hàm `doPost` sẽ kết nối đến Google Sheets và xử lý tin nhắn Telegram để ghi dữ liệu thu/chi vào Sheets.

```javascript
function doPost(e){
  // Kết nối đến bảng tính đích
  const ss = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('điền tên sheet của bạn vào đây')
  
  // Lấy nội dung từ payload của Telegram
  var contents = JSON.parse(e.postData.contents)
  
  // Lấy trường văn bản từ tin nhắn
  var text = contents.message.text
  
  // Loại bỏ "/collect" hoặc "/spend" khỏi tin nhắn
  var newText = text.substring(text.indexOf(' ') + 1)
  
  // Tách loại chi phí và số tiền
  var split = newText.split(',')

  // Nếu tìm thấy số tiền
  if(split[1] != null){

  // Thay thế đơn vị tr = 6 số 0, k = 3 số 0 (cách nhập nhanh số tiền)
  var number = split[1].replace('tr','000000').replace('k','000')

  // Nếu loại chi phí là "collect" (thu)
  if(text.includes('/collect')){
    // Thêm một dòng mới vào cột A, B, C
    const range = ss.appendRow([new Date(),split[0],number])
    // Lấy tổng thu nhập <đặt trong ô H1 dùng hàm SUM>
    const totalincome = ss.getRange("H1").getValue()
    
    // Gửi tin nhắn đến Telegram với liên kết đến Sheets
    sendMessage("Tổng thu nhập hiện tại là: " + totalincome + ". Liên kết để theo dõi thu nhập: <a href='Link đến bảng tính của bạn'>Sheets</a>")

  }
  // Tương tự với "/spend" (chi)
  else if(text.includes('/spend')){
    const range = ss.appendRow([new Date(),split[0],number])
    const totalexpenditure = ss.getRange("H2").getValue()
    sendMessage("Tổng chi hiện tại là: " + totalexpenditure + ". Liên kết để theo dõi chi tiêu: <a href='Link đến bảng tính của bạn'>Sheets</a>")
  }
  
} else
sendMessage("Sai cú pháp nhập, vui lòng nhập: " + "/collect, giá trị " + "hoặc " + "/spend, giá trị " + "Liên kết để theo dõi chi tiêu <a href='Link đến bảng tính của bạn'>Sheets</a>")
}
```

## Ảnh minh họa bot quản lý chi tiêu
![Output](/output.png)



