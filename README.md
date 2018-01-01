# Line-Bot-Basic 基礎Line Bot設定筆記

參考網站
--------------------------
- [LINE BOT 實戰 ( 原理篇 )](http://www.oxxostudio.tw/articles/201701/line-bot.html)
- [LINE BOT 實戰 ( 聊天篇 )](http://www.oxxostudio.tw/articles/201701/line-bot-2.html)
- [實戰智慧插座 29 - 用日幣匯率開關燈 ( Node.js 爬蟲應用篇 )](https://ithelp.ithome.com.tw/articles/10188884)

## 簡述
--------------------------
紀錄用從 0 開始設置一個簡單 bot 

## 環境
--------------------------
Node.js

## 步驟
--------------------------
1. 申請[Messaging API](https://developers.line.me/en/services/messaging-api/)
![設定畫面](https://screenshots.firefoxusercontent.com/images/7d9afe60-859d-4849-94b7-758f321127cc.png)

2. 申請[heroku app](https://dashboard.heroku.com/new-app)

3. 安裝 heroku cli 後 ```$ heroku login``` 登入帳號

4. clone先來本地端 ```heroku git:clone -a line-bot-newfuhua```

5. ```npm init``` 產生 package.json

6. package.json內增加 ```"start": "node ."``` 為 heroku 啟動的命令
```
{
  "name": "linechat",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "node ."
  },
  "author": "",
  "license": "ISC"
}
```

7. ```npm install linebot express --save``` 安裝模組

8. 建立一個 index.js，輸入下列指令，並填入自己 LINE BOT 的 channel Id、channel Secret 和 channel Access Token

```
var linebot = require('linebot');
var express = require('express');

var bot = linebot({
  channelId: channel Id,
  channelSecret: channel Secret,
  channelAccessToken: channel Access Token
});
```

9. 此code為收到 LINE 的訊息的時候把 event 印出，然後因為 express 預設走 port 3000，而 heroku 上預設卻不是，要透過另外一段程式碼作轉換
```
bot.on('message', function(event) {
  console.log(event); //把收到訊息的 event 印出來看看
});

const app = express();
const linebotParser = bot.parser();
app.post('/', linebotParser);

//因為 express 預設走 port 3000，而 heroku 上預設卻不是，要透過下列程式轉換
var server = app.listen(process.env.PORT || 8080, function() {
  var port = server.address().port;
  console.log("App now running on port", port);
});
```

10. 建立一個「.gitignore」的檔案， node_modules 的檔案是不用 push 上去

11. 第一階段 push 到 Heroku 上面
```
git add .
git commit -am "make it better"
git push heroku master
```

12. 回到 Heroku，看到 logs 的地方
![logs](http://www.oxxostudio.tw/img/articles/201701/20170115_1_16.jpg "logs")

13. 進入 Heroku 的 Settings 頁面，在 Config Vars 的地方填入 LINE BOT 的 ChannelAccessToken 和 ChannelSecret。( 記得不要有空格 )
![Config Vars](http://www.oxxostudio.tw/img/articles/201701/20170115_1_17.jpg "Config Vars")

14. 點選右上方的 Open APP，出現這個畫面，設定完成。
![Open APP](http://www.oxxostudio.tw/img/articles/201701/20170115_1_18.jpg "Open APP")

15. 從 Heroku 的 log 裡面看到 LINE 的訊息格式
```
{ type: 'message',
  replyToken: 'xxxxxxx',
  source: 
    { userId: 'xxxxxxx',
      type: 'user',
      profile: [Function] },
  timestamp: 1484472609833,
  message: 
    { type: 'text',
      id: 'xxxxxxxxxx',
      text: 'hihi',
      content: [Function] },
  reply: [Function] }
}
```

16. 匯率設置 參照[此篇](https://ithelp.ithome.com.tw/articles/10188884)
  * index.js中再加入這5個套件
  ```
  require("webduino-js");
  require("webduino-blockly");
  var request = require("request");
  var cheerio = require("cheerio");
  var nodemailer = require('nodemailer');
  ```

17. 待續...

