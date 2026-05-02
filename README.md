# gc-nq-relay
gc-nq-relay
const express = require('express');
const app = express();
app.use(express.json());

const WEBHOOK_SECRET = process.env.WEBHOOK_SECRET || 'gold2026secure';

let latestAlert = null;

// 接收 TradingView Alert
app.post('/webhook', (req, res) => {
  const { secret, ...data } = req.body;
  
  if (secret !== WEBHOOK_SECRET) {
    return res.status(403).json({ error: 'Unauthorized' });
  }
  
  latestAlert = { ...data, timestamp: new Date().toISOString() };
  console.log('Alert received:', latestAlert);
  res.json({ status: 'ok' });
});

// 瀏覽器輪詢用
app.get('/latest', (req, res) => {
  res.json(latestAlert || { message: 'No alert yet' });
});

// Render 免費方案心跳（防休眠）
app.get('/ping', (req, res) => res.send('pong'));

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
