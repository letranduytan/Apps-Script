//https://api.telegram.org/bot<token>/METHOD_NAME

var webapp = 'put your web app url here' 
var token = 'put your bot token here'; 
var url = 'https://api.telegram.org/bot' + token;
var chatid = 'put your chat id here';

function setWebhook(){
  var response = UrlFetchApp.fetch(url + '/setWebhook?url=' + webapp) 
  Logger.log(response)
}

function sendMessage(body){
  var response = UrlFetchApp.fetch(url + '/sendMessage?chat_id=' + chatid + '&text=' + encodeURIComponent(body) + '&parse_mode=HTML')
}

function doPost(e){
  const ss = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('put your sheet name here')
 
  var contents = JSON.parse(e.postData.contents)
  
  var text = contents.message.text
  
  var newText = text.substring(text.indexOf(' ') + 1)
  
  var split = newText.split(',')

if(split[1] != null){

  var number = split[1].replace('tr','000000').replace('k','000')

  if(text.includes('/thu')){
    const range = ss.appendRow([new Date(),split[0],number])
    const tongthu = ss.getRange("H1").getValue()
    sendMessage("Tổng thu hiện tại là: " + tongthu + ". Link theo dõi khoản thu nà: <a href='Link to your sheets'>Sheets</a>")
  }
  else if(text.includes('/chi')){
    const range = ss.appendRow([new Date(),split[0],,number])
    const tongchi = ss.getRange("H2").getValue()
    sendMessage("Tổng chi hiện tại là:  " + tongchi + ". Link theo dõi nà: <a href='Link to your sheets'>Sheets</a>")
    
  }
  
} else 
sendMessage("Hình như nhập sai cú pháp " + "/thu loại hình, giá " + "hoặc " + "/chi loại hình, giá " + "rồi," + " hay là muốn theo dõi <a href='Link to your sheets'>Sheets này</a>?")
}





