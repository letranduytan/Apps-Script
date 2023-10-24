# Apps-Script
//by: tiktok.com/@lil.excel
//Sau khi điền webapp url vào, chạy function setWebhook / After pasting webapp url, run function setWebhook first

//method: https://api.telegram.org/bot<token>/METHOD_NAME

var webapp = 'put your web app url here' //điền web app url có được khi deploy vào vd: var webapp ='123abc'
var token = 'put your bot token here'; //điền bot token lấy được sau khi nói chuyện với fatherbot
var url = 'https://api.telegram.org/bot' + token;
var chatid = 'put your chat id here';

/*Để lấy thông tin về chat id cá nhân (tự nói chuyện với bot), sử dụng @userinfobot / For personal chat id, use @userinfobot to get
Để lấy thông tin về chat id nhóm (nhiều người có thể dùng bot), làm theo các bước / For group chat id, follow these steps:
1. Thêm bot vào group mới / Add bot to a new group
2. Tag con bot nói 1 2 câu (@yourbot hello) / Say something mentioning the bot
3. Dán đoạn sau đây vào trình duyệt (lưu ý nhập bot token) / Paste this url to your browser (with your bot token):
https://api.telegram.org/bot<token>/getUpdates
4. Lấy chatid / Get the chatid
*/

//sử dụng để "móc" telegram bot với sheets / Connect sheets & tele
function setWebhook(){
  var response = UrlFetchApp.fetch(url + '/setWebhook?url=' + webapp) 
  Logger.log(response)
}

//sử dụng để gửi tin nhắn từ bot về kênh / Make bot send message to you (or to group)
function sendMessage(body){
  var response = UrlFetchApp.fetch(url + '/sendMessage?chat_id=' + chatid + '&text=' + encodeURIComponent(body) + '&parse_mode=HTML')
}

//sử dụng để tương tác telegram với google sheets / Control sheets from telegram
function doPost(e){
  //kết nối tới sheet / connect to destinate sheet
  const ss = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('put your sheet name here')
  //lấy payload đẩy từ telegram lên / get the contents from telegram payload
  var contents = JSON.parse(e.postData.contents)
  
  //lấy trường thông tin chứa dữ liệu text / get the text field
  var text = contents.message.text
  
  //bỏ qua "/thu" hoặc "/chi", có thể dùng replace thay thế tùy logic / to remove "/thu" or "/chi", can use replace or other ways
  var newText = text.substring(text.indexOf(' ') + 1)
  
  //tách danh mục và khoản thu chi / split category and received/paid amount
  var split = newText.split(',')

//Nếu tách được, đúng cú pháp / if the split could found the amount
if(split[1] != null){

  //Thay thế "tr" bằng 000000, "k" bằng 000 -> 100tr = 100000000 / Replace tr = 6x0, k = 3x0 (Vietnamese way to quick entering amount)
  var number = split[1].replace('tr','000000').replace('k','000')

  //Nếu danh mục là thu / If category is "thu"
  if(text.includes('/thu')){
    //Thêm 1 dòng mới vào cuối cột A, B, C / Append a new row at the end of column A, B, C
    const range = ss.appendRow([new Date(),split[0],number])
    //Lấy tổng thu setup sẵn ở ô H1 bằng hàm SUM(C:C) / Get total received <setup in cell H1 using SUM function> 
    const tongthu = ss.getRange("H1").getValue()
    
    //Gửi 1 message về telegram kèm link tới sheets / Send a message to telegram with link to sheets
    sendMessage("Tổng thu hiện tại là: " + tongthu + ". Link theo dõi khoản thu nà: <a href='Link to your sheets'>Sheets</a>")

    
  }
  //Tương tự với chi / Same with "/chi"
  else if(text.includes('/chi')){
    const range = ss.appendRow([new Date(),split[0],,number])
    const tongchi = ss.getRange("H2").getValue()
    sendMessage("Tổng chi hiện tại là:  " + tongchi + ". Link theo dõi nà: <a href='Link to your sheets'>Sheets</a>")
    
  }
  
} else 
sendMessage("Hình như nhập sai cú pháp " + "/thu loại hình, giá " + "hoặc " + "/chi loại hình, giá " + "rồi," + " hay là muốn theo dõi <a href='Link to your sheets'>Sheets này</a>?")
}