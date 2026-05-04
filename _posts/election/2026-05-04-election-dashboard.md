---
title: ELECTION DASHBOARD
date: 2026-05-04T16:20:00.000Z
---
<!DOCTYPE html>
<html>
<head>
<title>Election Dashboard</title>

<style>
body {
  font-family: Arial, sans-serif;
  margin: 0;
  background: #f5f6fa;
}

/* HEADER */
.header {
  text-align: center;
  padding: 15px;
  background: white;
  border-bottom: 1px solid #ddd;
}

/* GRID */
.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 15px;
  padding: 20px;
}

/* CARD */
.card {
  background: white;
  border-radius: 12px;
  padding: 15px;
  box-shadow: 0 3px 8px rgba(0,0,0,0.08);
}

/* STATE */
.state {
  font-size: 20px;
  font-weight: bold;
  text-align: center;
}

/* ROW */
.row {
  display: flex;
  justify-content: space-between;
  padding: 8px;
  margin: 5px 0;
  background: #fafafa;
  border-radius: 6px;
}

/* PHOTO */
img {
  width: 30px;
  height: 30px;
  border-radius: 50%;
  object-fit: cover;
  margin-right: 8px;
}

/* TICKER */
.ticker {
  background: #e53935;
  color: white;
  padding: 10px;
  overflow: hidden;
  white-space: nowrap;
}

#tickerText {
  display: inline-block;
  padding-left: 100%;
  animation: scroll 15s linear infinite;
}

@keyframes scroll {
  from { transform: translateX(0); }
  to { transform: translateX(-100%); }
}

</style>
</head>

<body>

<div class="header">
  <h2>Election Dashboard</h2>
</div>

<div class="container" id="container"></div>

<div class="ticker">
  <div id="tickerText">Loading...</div>
</div>

<script>

/* ✅ YOUR CORRECT API URL */
const API = "https://script.google.com/macros/s/AKfycbw-eSy_9rUII7z-GZ0txpJVFEN0WRTSVgdQfqKEPhweeaIadMB-jJeSP8OMWMkWIBbZ/exec";

/* 🎨 COLORS */
const COLORS = {
  "LDF": "#e53935",
  "UDF": "#1e88e5",
  "NDA": "#fb8c00",
  "BJP": "#fb8c00",
  "Congress": "#1976d2",
  "DMK": "#c62828",
  "AIADMK": "#2e7d32",
  "TMC": "#2e7d32"
};

/* 📸 AUTO PHOTO */
function getPhoto(row){
  const photos = {
    "LDF": "https://upload.wikimedia.org/wikipedia/commons/5/55/Pinarayi_Vijayan_2016.jpg",
    "UDF": "https://upload.wikimedia.org/wikipedia/commons/3/3f/VD_Satheesan.jpg",
    "NDA": "https://upload.wikimedia.org/wikipedia/commons/9/9e/Himanta_Biswa_Sarma.jpg",
    "Congress": "https://upload.wikimedia.org/wikipedia/commons/3/3e/Gaurav_Gogoi.jpg"
  };
  return row.PhotoURL || photos[row.Alliance] || "https://i.pravatar.cc/150?img=10";
}

/* 📊 LOAD DATA */
function loadData(){

  fetch(API)
  .then(res => res.json())
  .then(data => {

    let grouped = {};

    data.forEach(r=>{
      if(!grouped[r.State]) grouped[r.State] = [];
      grouped[r.State].push(r);
    });

    let html = "";

    Object.keys(grouped).forEach(state=>{

      html += `<div class="card">`;
      html += `<div class="state">${state}</div>`;

      grouped[state].forEach(row=>{
        let color = COLORS[row.Alliance] || "#999";

        html += `
          <div class="row" style="border-left:4px solid ${color}">
            <div style="display:flex;align-items:center;">
              <img src="${getPhoto(row)}">
              ${row.Alliance}
            </div>
            <div>${row.Seats}</div>
          </div>
        `;
      });

      html += `</div>`;
    });

    container.innerHTML = html;

    /* 📢 TICKER */
    if(data[0] && data[0].Ticker){
      tickerText.innerText = data[0].Ticker;
    }

  })
  .catch(err=>{
    container.innerHTML = "❌ API Error";
    console.log(err);
  });
}

/* 🔄 AUTO REFRESH */
setInterval(loadData, 10000);
loadData();

</script>

</body>
</html>