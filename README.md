# Connect telegram with google sheets

by LeTranDuyTan
 
-----------------------------------------------------------------------------------------------
**Contact**
* [Facebook](https://www.facebook.com/duytan.hh) 
* [X](https://twitter.com/12dtan) 
* [Reddit](https://www.reddit.com/user/DuYTano3)

-----------------------------------------------------------------------------------------------

After pasting webapp url, run function setWebhook first

Method: https://api.telegram.org/bot<token>/METHOD_NAME

```javascript
var webapp = 'put your web app url here' 
var token = 'put your bot token here'; 
var url = 'https://api.telegram.org/bot' + token;
var chatid = 'put your chat id here';
```

For personal chat id, use @userinfobot to get, For group chat id, follow these steps:
1. Add bot to a new group
2. Say something mentioning the bot
3. Paste this url to your browser (with your bot token):
https://api.telegram.org/bot<token>/getUpdates
4. Get the chatid

## Connect sheets & tell

```javascript
function setWebhook(){
      var response = UrlFetchApp.fetch(url + '/setWebhook?url=' + webapp) 
      Logger.log(response)
    }
```

## Make bot send message to you (or to group)

```javascript
function sendMessage(body){
  var response = UrlFetchApp.fetch(url + '/sendMessage?chat_id=' + chatid + '&text=' + encodeURIComponent(body) + '&parse_mode=HTML')
}
```

## Control sheets from telegram

```javascript
function doPost(e){
  //connect to destinate sheet
  const ss = SpreadsheetApp.getActiveSpreadsheet().getSheetByName('put your sheet name here')
  //get the contents from telegram payload
  var contents = JSON.parse(e.postData.contents)
  
  //get the text field
  var text = contents.message.text
  
  //to remove "/collect" or "/spend", can use replace or other ways
  var newText = text.substring(text.indexOf(' ') + 1)
  
  //split category and received/paid amount
  var split = newText.split(',')

  //if the split could found the amount
  if(split[1] != null){

  //Replace tr = 6x0, k = 3x0 (Vietnamese way to quick entering amount)
  var number = split[1].replace('tr','000000').replace('k','000')

  //If category is "collect"
  if(text.includes('/collect')){
    //Append a new row at the end of column A, B, C
    const range = ss.appendRow([new Date(),split[0],number])
    //Get total received <setup in cell H1 using SUM function> 
    const totalincome = ss.getRange("H1").getValue()
    
    //Send a message to telegram with link to sheets
    sendMessage("Current total revenue is: " + totalincome + ". Link to track revenue: <a href='Link to your sheets'>Sheets</a>")

    
  }
  //Same with "/spend"
  else if(text.includes('/spend')){
    const range = ss.appendRow([new Date(),split[0],,number])
    const totalexpenditure = ss.getRange("H2").getValue()
    sendMessage("Current total expenditure is:  " + totalexpenditure + ". Link to track expenses: <a href='Link to your sheets'>Sheets</a>")
    
  }
  
} else
sendMessage("Entered incorrect syntax" + "/collect, price " + "or " + "/spend, price " + " Link to track spending <a href='Link to your sheets'>Sheets này</a>?")
}
```
