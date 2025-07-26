# -// index.js
const express = require('express');
const line = require('@line/bot-sdk');

const config = {
  channelAccessToken: '你的 Channel Access Token',
  channelSecret: '你的 Channel Secret',
};

const app = express();
const client = new line.Client(config);

app.post('/webhook', line.middleware(config), (req, res) => {
  Promise
    .all(req.body.events.map(handleEvent))
    .then((result) => res.json(result));
});

function handleEvent(event) {
  if (event.type !== 'message' || event.message.type !== 'text') {
    return Promise.resolve(null);
  }

  const userId = event.source.userId;
  const messageText = event.message.text;

  if (messageText === '打卡') {
    const now = new Date().toLocaleString();
    // 👉 可在這裡寫入資料庫
    return client.replyMessage(event.replyToken, {
      type: 'text',
      text: `✅ 打卡成功！\n時間：${now}`,
    });
  } else {
    return client.replyMessage(event.replyToken, {
      type: 'text',
      text: '請輸入「打卡」來記錄今天的打卡時間！',
    });
  }
}

app.listen(process.env.PORT || 3000, () => {
  console.log('伺服器已啟動');
});
