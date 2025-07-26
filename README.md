const express = require('express');
const line = require('@line/bot-sdk');

const config = {
  channelAccessToken: '你的 Channel Access Token',
  channelSecret: '你的 Channel Secret',
};

const app = express();
const client = new line.Client(config);

// 接收 webhook 的路由
app.post('/webhook', line.middleware(config), (req, res) => {
  Promise
    .all(req.body.events.map(handleEvent))
    .then((result) => res.json(result))
    .catch((err) => {
      console.error(err);
      res.status(500).end();
    });
});

// 處理事件的函式
function handleEvent(event) {
  // 只處理文字訊息
  if (event.type !== 'message' || event.message.type !== 'text') {
    return Promise.resolve(null);
  }

  const userId = event.source.userId;
  const messageText = event.message.text;

  if (messageText === '打卡') {
    const now = new Date().toLocaleString();
    const replyText = `✅ 打卡成功！\n時間：${now}`;

    // 🔧 這裡可以加上寫入資料庫的邏輯

    return client.replyMessage(event.replyToken, {
      type: 'text',
      text: replyText,
    });
  } else {
    return client.replyMessage(event.replyToken, {
      type: 'text',
      text: '請輸入「打卡」來記錄今天的打卡時間！',
    });
  }
}

// 啟動伺服器
app.listen(process.env.PORT || 3000, () => {
  console.log('伺服器已啟動');
});
