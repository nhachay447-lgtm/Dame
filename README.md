<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Tool Auto Report FB - Web HTML</title>
  <style>
    * { box-sizing: border-box; font-family: Arial, sans-serif; }
    body {
      margin: 0;
      padding: 20px;
      background: #0f172a;
      color: #e2e8f0;
    }
    .container {
      max-width: 840px;
      margin: 0 auto;
    }
    .box {
      background: #1e293b;
      border: 1px solid #334155;
      border-radius: 10px;
      padding: 18px;
      margin-bottom: 16px;
    }
    h2 { margin: 0 0 12px; font-size: 21px; color: #38bdf8; }
    h3 { margin: 0 0 10px; font-size: 16px; color: #f8fafc; }
    label { display: block; margin-top: 10px; font-size: 14px; font-weight: bold; }
    select, input, textarea {
      width: 100%;
      margin-top: 6px;
      padding: 10px;
      border-radius: 6px;
      border: 1px solid #475569;
      background: #0f172a;
      color: #f8fafc;
    }
    .row {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 12px;
    }
    @media (max-width: 640px) {
      .row { grid-template-columns: 1fr; }
    }
    .info-grid {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 10px;
      margin-top: 12px;
    }
    @media (max-width: 640px) {
      .info-grid { grid-template-columns: 1fr; }
    }
    .info-card {
      background: #0f172a;
      border: 1px solid #334155;
      border-radius: 8px;
      padding: 10px 12px;
    }
    .info-label {
      font-size: 12px;
      color: #94a3b8;
      margin-bottom: 4px;
    }
    .info-val {
      font-size: 14px;
      font-weight: bold;
      color: #38bdf8;
      word-break: break-all;
    }
    .timer-val {
      font-family: "Courier New", monospace;
      font-size: 18px;
      color: #4ade80;
    }
    button, a.btn {
      display: inline-block;
      margin-top: 14px;
      margin-right: 10px;
      padding: 10px 16px;
      border-radius: 6px;
      border: none;
      background: #0284c7;
      color: #fff;
      font-weight: bold;
      text-decoration: none;
      cursor: pointer;
    }
    a.btn.green { background: #16a34a; }
    button.amber { background: #d97706; }
    button.red { background: #dc2626; }
    button.gray { background: #475569; }
    .hint { font-size: 13px; color: #94a3b8; margin-top: 8px; line-height: 1.5; }
  </style>
</head>
<body>
  <div class="container">
    <h2>Bảng điều khiển kịch bản (Web HTML)</h2>

    <div class="box">
      <h3>Thông tin mục tiêu &amp; Bộ đếm giờ</h3>
      <label for="targetInput">Dán Link Profile / Page hoặc nhập UID:</label>
      <input
        id="targetInput"
        type="text"
        placeholder="Ví dụ: https://www.facebook.com/profile.php?id=1000123456789 hoặc 1000123456789"
      />

      <div class="info-grid">
        <div class="info-card">
          <div class="info-label">UID / Định danh nhận diện</div>
          <div id="uidDisplay" class="info-val">Chưa có</div>
        </div>
        <div class="info-card">
          <div class="info-label">Thông tin trang / Profile</div>
          <div id="profileInfoDisplay" class="info-val">Chưa nhập mục tiêu</div>
        </div>
        <div class="info-card">
          <div class="info-label">Bộ đếm thời gian</div>
          <div id="timerDisplay" class="info-val timer-val">00:00</div>
        </div>
      </div>

      <button id="stopTimerBtn" class="btn amber" type="button">Dừng</button>
      <button id="offTimerBtn" class="btn red" type="button">Tắt</button>
      <button id="resetTimerBtn" class="btn gray" type="button">Đặt lại</button>
    </div>

    <div class="box">
      <label for="modeSelect">Chọn bộ kịch bản (STEPS):</label>
      <select id="modeSelect">
        <option value="STEPS_DAME_NOT">STEPS_DAME_NOT</option>
        <option value="STEPS_LOCKED">STEPS_LOCKED</option>
        <option value="STEPS_POST">STEPS_POST</option>
      </select>

      <div class="row">
        <div>
          <label for="loopInput">Số lần lặp vòng:</label>
          <input id="loopInput" type="number" min="1" value="1" />
        </div>
        <div>
          <label for="delayInput">Thời gian chờ mỗi bước (ms):</label>
          <input id="delayInput" type="number" min="300" step="100" value="1500" />
        </div>
      </div>

      <button id="genBtn" type="button">Tạo mã &amp; Bookmarklet</button>
      <button id="copyBtn" type="button">Copy mã chạy</button>
    </div>

    <div class="box">
      <h3>Cách chạy trên trình duyệt</h3>
      <p class="hint">
        1. Nhập link/UID, chọn kịch bản rồi bấm <b>Tạo mã &amp; Bookmarklet</b>.<br />
        2. Kéo nút xanh <b>Kéo lên dấu trang</b> lên thanh Bookmark, mở trang Facebook mục tiêu rồi bấm vào dấu trang đó.
      </p>
      <a id="bookmarkBtn" class="btn green" href="javascript:void(0)">Kéo lên dấu trang</a>
      <label for="outputCode">Mã thực thi tự sinh:</label>
      <textarea id="outputCode" rows="8" readonly></textarea>
    </div>
  </div>

  <script>
    const STEPS_DAME_NOT = [
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Violent, hateful or disturbing content", l: "Chọn Violent, hateful..." },
      { q: "Seems like terrorism", l: "Chọn Seems like terrorism" },
      { q: "Submit", l: "Nhấn Submit" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Violent, hateful or disturbing content", l: "Chọn Violent, hateful..." },
      { q: "Calling for violence", l: "Chọn Calling for violence" },
      { q: "Submit", l: "Nhấn Submit" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Violent, hateful or disturbing content", l: "Chọn Violent, hateful..." },
      { q: "Seems like organized crime", l: "Chọn Seems like organized crime" },
      { q: "Submit", l: "Nhấn Submit" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Suicide or self-harm", l: "Chọn Suicide or self-harm" },
      { q: "Suicide or self-injury", l: "Chọn Suicide or self-injury" },
      { q: "Submit", l: "Nhấn Submit" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Scam, fraud or false information", l: "Chọn Scam, fraud..." },
      { q: "Fraud or scam", l: "Chọn Fraud or scam" },
      { q: "Submit", l: "Nhấn Submit" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Scam, fraud or false information", l: "Chọn Scam, fraud..." },
      { q: "Spam", l: "Chọn Spam" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Fake profile" },
      { q: "Fake profile", l: "Chọn Fake profile" },
      { q: "They’re not a real person", l: "Chọn They’re not..." },
      { q: "Submit", l: "Nhấn Submit" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about..." },
      { q: "Something else", l: "Chọn Something else" },
      { q: "Done", l: "Chọn Done" }
    ];

    const STEPS_LOCKED = [
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Scam, fraud or false information", l: "Chọn Scam, fraud..." },
      { q: "Fraud or scam", l: "Chọn Fraud or scam" },
      { q: "Submit", l: "Nhấn Submit" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Scam, fraud or false information", l: "Chọn Scam, fraud..." },
      { q: "Fraud or scam", l: "Chọn Fraud or scam" },
      { q: "Submit", l: "Nhấn Submit" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Scam, fraud or false information", l: "Chọn Scam, fraud..." },
      { q: "Fraud or scam", l: "Chọn Fraud or scam" },
      { q: "Submit", l: "Nhấn Submit" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Scam, fraud or false information", l: "Chọn Scam, fraud..." },
      { q: "Spam", l: "Chọn Spam" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Scam, fraud or false information", l: "Chọn Scam, fraud..." },
      { q: "Spam", l: "Chọn Spam" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Scam, fraud or false information", l: "Chọn Scam, fraud..." },
      { q: "Spam", l: "Chọn Spam" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Scam, fraud or false information", l: "Chọn Scam, fraud..." },
      { q: "Sharing false information", l: "Chọn Sharing false information" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Scam, fraud or false information", l: "Chọn Scam, fraud..." },
      { q: "Sharing false information", l: "Chọn Sharing false information" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Scam, fraud or false information", l: "Chọn Scam, fraud..." },
      { q: "Sharing false information", l: "Chọn Sharing false information" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Scam, fraud or false information", l: "Chọn Scam, fraud or false information" },
      { q: "Fraud or scam", l: "Chọn Fraud or scam" },
      { q: "Submit", l: "Nhấn Submit" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Scam, fraud or false information", l: "Chọn Scam, fraud or false information" },
      { q: "Spam", l: "Chọn Spam" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Suicide or self-harm", l: "Chọn Suicide or self-harm" },
      { q: "Eating disorder", l: "Chọn Eating disorder" },
      { q: "Submit", l: "Nhấn Submit" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Violent, hateful or disturbing content", l: "Chọn Violent, hateful..." },
      { q: "Seems like terrorism", l: "Chọn Seems like terrorism" },
      { q: "Submit", l: "Nhấn Submit" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Violent, hateful or disturbing content", l: "Chọn Violent, hateful..." },
      { q: "Seems like organized crime", l: "Chọn Seems like organized..." },
      { q: "Submit", l: "Nhấn Submit" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Suicide or self-harm", l: "Chọn Suicide or self-harm" },
      { q: "Eating disorder", l: "Chọn Eating disorder" },
      { q: "Submit", l: "Nhấn Submit" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Adult content", l: "Chọn Adult content" },
      { q: "Seems like prostitution", l: "Chọn Seems like prostitution" },
      { q: "Submit", l: "Nhấn Submit" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Fake profile", l: "Chọn Fake profile" },
      { q: "They’re not a real person", l: "They’re not a real person" },
      { q: "Submit", l: "Nhấn Submit" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Something else", l: "Chọn Something else" },
      { q: "Done", l: "Nhấn Done" }
    ];

    const STEPS_POST = [
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "A specific post", l: "Chọn A specific post" },
      { type: "pick_post", l: "Đang tự động chọn bài viết (Mới nhất)" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Scam, fraud or false information", l: "Chọn Scam, fraud..." },
      { q: "Fraud or scam", l: "Chọn Fraud or scam" },
      { q: "Submit", l: "Nhấn Submit" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "A specific post", l: "Chọn A specific post" },
      { type: "pick_post", l: "Đang tự động chọn bài viết (Mới nhất)" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Scam, fraud or false information", l: "Chọn Scam, fraud..." },
      { q: "Fraud or scam", l: "Chọn Fraud or scam" },
      { q: "Submit", l: "Nhấn Submit" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "A specific post", l: "Chọn A specific post" },
      { type: "pick_post", l: "Đang tự động chọn bài viết (Mới nhất)" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Suicide or self-harm", l: "Chọn Suicide or self-harm" },
      { q: "Suicide or self-injury", l: "Chọn Suicide or self-injury" },
      { q: "Submit", l: "Nhấn Submit" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Scam, fraud or false information", l: "Chọn Scam, fraud..." },
      { q: "Fraud or scam", l: "Chọn Fraud or scam" },
      { q: "Submit", l: "Nhấn Submit" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Scam, fraud or false information", l: "Chọn Scam, fraud..." },
      { q: "Fraud or scam", l: "Chọn Fraud or scam" },
      { q: "Submit", l: "Nhấn Submit" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Scam, fraud or false information", l: "Chọn Scam, fraud..." },
      { q: "Fraud or scam", l: "Chọn Fraud or scam" },
      { q: "Submit", l: "Nhấn Submit" },
      { q: "Next", l: "Nhấn Next" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Scam, fraud or false information", l: "Chọn Scam, fraud..." },
      { q: "Spam", l: "Chọn Spam" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Scam, fraud or false information", l: "Chọn Scam, fraud..." },
      { q: "Spam", l: "Chọn Spam" },
      { q: "Done", l: "Nhấn Done" },
      { q: "Profile settings see more options", l: "Mở Menu" },
      { q: "Report profile", l: "Chọn Report profile" },
      { q: "Something about this profile", l: "Chọn Something about this profile" },
      { q: "Scam, fraud or false information", l: "Chọn Scam, fraud..." },
      { q: "Spam", l: "Chọn Spam" },
      { q: "Done", l: "Nhấn Done" }
    ];

    const PRESETS = { STEPS_DAME_NOT, STEPS_LOCKED, STEPS_POST };

    function parseTarget(raw) {
      const text = (raw || "").trim();
      if (!text) {
        return { raw: "", uid: "Chưa có", info: "Chưa nhập mục tiêu" };
      }
      if (/^\d{5,}$/.test(text)) {
        return { raw: text, uid: text, info: "UID số trực tiếp" };
      }
      try {
        const url = new URL(text.startsWith("http") ? text : "https://" + text);
        const idParam = url.searchParams.get("id");
        if (idParam && /^\d+$/.test(idParam)) {
          return { raw: url.href, uid: idParam, info: "Profile ID (" + url.hostname + ")" };
        }
        const pathParts = url.pathname.split("/").filter(Boolean);
        const numMatch = url.href.match(/(?:profile\.php\?id=|\/)(\d{6,})(?:\/|$|\?)/);
        const slug = pathParts[0] || url.hostname;
        return {
          raw: url.href,
          uid: numMatch ? numMatch[1] : slug,
          info: "Đường dẫn: /" + pathParts.join("/")
        };
      } catch {
        const numMatch = text.match(/(\d{6,})/);
        return {
          raw: text,
          uid: numMatch ? numMatch[1] : text,
          info: "Chuỗi định danh tùy chỉnh"
        };
      }
    }

    let elapsedSeconds = 0;
    let isTimerRunning = true;

    function formatTime(sec) {
      const m = String(Math.floor(sec / 60)).padStart(2, "0");
      const s = String(sec % 60).padStart(2, "0");
      return m + ":" + s;
    }

    setInterval(() => {
      if (!isTimerRunning) return;
      elapsedSeconds += 1;
      document.getElementById("timerDisplay").textContent = formatTime(elapsedSeconds);
    }, 1000);

    document.getElementById("stopTimerBtn").addEventListener("click", () => {
      isTimerRunning = false;
    });

    document.getElementById("offTimerBtn").addEventListener("click", () => {
      isTimerRunning = false;
      elapsedSeconds = 0;
      document.getElementById("timerDisplay").textContent = "OFF";
    });

    document.getElementById("resetTimerBtn").addEventListener("click", () => {
      elapsedSeconds = 0;
      isTimerRunning = true;
      document.getElementById("timerDisplay").textContent = "00:00";
    });

    function refreshTargetInfo() {
      const info = parseTarget(document.getElementById("targetInput").value);
      document.getElementById("uidDisplay").textContent = info.uid;
      document.getElementById("profileInfoDisplay").textContent = info.info;
      return info;
    }

    function buildRunnerScript(config) {
      return `(async function(){
        if (window.__FB_AUTO_REPORT_CLEANUP__) {
          window.__FB_AUTO_REPORT_CLEANUP__();
        }
        const cfg = ${JSON.stringify(config)};
        const sleep = (ms) => new Promise((r) => setTimeout(r, ms));
        const norm = (s) => (s || "").replace(/\\s+/g, " ").trim().toLowerCase();

        function detectPageInfo() {
          const href = location.href;
          const urlId = new URLSearchParams(location.search).get("id");
          const htmlMatch = document.documentElement.innerHTML.match(/"userID":"(\\d+)"|"entity_id":"(\\d+)"/);
          const uid = (urlId && /^\\d+$/.test(urlId)) ? urlId : (htmlMatch ? (htmlMatch[1] || htmlMatch[2]) : (cfg.target.uid || "Không rõ"));
          const heading = document.querySelector("h1");
          const name = (heading && heading.innerText.trim()) || document.title || "Trang Facebook";
          return { uid, name, href };
        }

        const pageInfo = detectPageInfo();
        let sec = 0;
        let stopped = false;
        let currentStatus = "Đang khởi tạo...";

        const box = document.createElement("div");
        box.id = "fb-auto-report-box";
        box.style.cssText = "position:fixed;bottom:16px;right:16px;z-index:999999;background:#0f172a;color:#f8fafc;border:1px solid #38bdf8;border-radius:10px;padding:12px 14px;font-family:Arial,sans-serif;font-size:12px;line-height:1.5;box-shadow:0 6px 20px rgba(0,0,0,0.45);max-width:310px;";
        box.innerHTML = "<div style='font-weight:bold;color:#38bdf8;margin-bottom:4px;'>Auto Report Status</div><div id='ar-meta'></div><div id='ar-step' style='margin-top:4px;color:#cbd5e1;'></div><div id='ar-time' style='margin-top:4px;color:#4ade80;font-weight:bold;'></div><div style='margin-top:8px;display:flex;gap:8px;'><button id='ar-stop-btn' type='button' style='flex:1;padding:6px 10px;border:none;border-radius:6px;background:#d97706;color:#fff;font-weight:bold;cursor:pointer;'>Dừng</button><button id='ar-off-btn' type='button' style='flex:1;padding:6px 10px;border:none;border-radius:6px;background:#dc2626;color:#fff;font-weight:bold;cursor:pointer;'>Tắt</button></div>";
        document.body.appendChild(box);

        function renderBox() {
          const metaEl = box.querySelector("#ar-meta");
          const stepEl = box.querySelector("#ar-step");
          const timeEl = box.querySelector("#ar-time");
          if (metaEl) metaEl.textContent = "Profile: " + pageInfo.name + " | UID: " + pageInfo.uid;
          if (stepEl) stepEl.textContent = currentStatus;
          if (timeEl) {
            const mm = String(Math.floor(sec / 60)).padStart(2, "0");
            const ss = String(sec % 60).padStart(2, "0");
            timeEl.textContent = "Thời gian chạy: " + mm + ":" + ss;
          }
        }

        const clock = setInterval(() => {
          if (stopped) return;
          sec += 1;
          renderBox();
        }, 1000);

        function stopRun() {
          stopped = true;
          currentStatus = "Đã dừng bởi người dùng.";
          renderBox();
        }

        function turnOffRun() {
          stopped = true;
          clearInterval(clock);
          if (box && box.parentNode) box.parentNode.removeChild(box);
          if (window.__FB_AUTO_REPORT_CLEANUP__ === turnOffRun) {
            delete window.__FB_AUTO_REPORT_CLEANUP__;
          }
        }

        window.__FB_AUTO_REPORT_CLEANUP__ = turnOffRun;
        box.querySelector("#ar-stop-btn").addEventListener("click", stopRun);
        box.querySelector("#ar-off-btn").addEventListener("click", turnOffRun);
        renderBox();

        function findTarget(queryText) {
          const target = norm(queryText);
          const candidates = Array.from(document.querySelectorAll('button, [role="button"], [role="menuitem"], [role="radio"], [role="checkbox"], a, span, div'));
          for (const el of candidates) {
            const aria = norm(el.getAttribute("aria-label"));
            const text = norm(el.innerText);
            if ((aria && aria.includes(target)) || (text && text === target)) {
              const clickable = el.closest('button, [role="button"], [role="menuitem"], [role="radio"], [role="checkbox"], a') || el;
              const rect = clickable.getBoundingClientRect();
              if (rect.width > 0 && rect.height > 0) return clickable;
            }
          }
          for (const el of candidates) {
            const text = norm(el.innerText);
            if (text && text.includes(target) && text.length <= target.length + 25) {
              const clickable = el.closest('button, [role="button"], [role="menuitem"], [role="radio"], [role="checkbox"], a') || el;
              const rect = clickable.getBoundingClientRect();
              if (rect.width > 0 && rect.height > 0) return clickable;
            }
          }
          return null;
        }

        function pickFirstPost() {
          const articles = Array.from(document.querySelectorAll('[role="article"]'));
          if (!articles.length) return false;
          const postEl = articles[0].querySelector('button, [role="button"], [role="radio"], [role="checkbox"], label') || articles[0];
          postEl.scrollIntoView({ behavior: "smooth", block: "center" });
          postEl.click();
          return true;
        }

        for (let loop = 1; loop <= cfg.loopCount; loop++) {
          if (stopped) break;
          for (let i = 0; i < cfg.steps.length; i++) {
            if (stopped) break;
            const step = cfg.steps[i];
            currentStatus = "Vòng " + loop + "/" + cfg.loopCount + " | Bước " + (i + 1) + "/" + cfg.steps.length + ": " + (step.l || step.q || step.type);
            renderBox();
            if (step.type === "pick_post") {
              pickFirstPost();
            } else if (step.q) {
              const el = findTarget(step.q);
              if (el) {
                el.scrollIntoView({ behavior: "smooth", block: "center" });
                el.click();
              } else {
                console.warn("[AutoReport] Không thấy phần tử: " + step.q);
              }
            }
            await sleep(cfg.delayMs);
          }
        }

        if (!stopped) {
          currentStatus = "Hoàn tất (" + cfg.loopCount + " vòng).";
          renderBox();
          clearInterval(clock);
        }
      })();`;
    }

    function updateGenerated() {
      const target = refreshTargetInfo();
      const mode = document.getElementById("modeSelect").value;
      const loopCount = Math.max(1, Number(document.getElementById("loopInput").value) || 1);
      const delayMs = Math.max(300, Number(document.getElementById("delayInput").value) || 1500);
      const steps = PRESETS[mode] || STEPS_DAME_NOT;
      const code = buildRunnerScript({ target, mode, loopCount, delayMs, steps });

      document.getElementById("outputCode").value = code;
      document.getElementById("bookmarkBtn").href = "javascript:" + encodeURIComponent(code);
    }

    document.getElementById("targetInput").addEventListener("input", updateGenerated);
    document.getElementById("genBtn").addEventListener("click", updateGenerated);
    document.getElementById("copyBtn").addEventListener("click", async () => {
      updateGenerated();
      const val = document.getElementById("outputCode").value;
      try {
        await navigator.clipboard.writeText(val);
        alert("Đã copy mã chạy!");
      } catch {
        alert("Hãy chọn vùng mã và copy thủ công.");
      }
    });

    updateGenerated();
  </script>
</body>
</html>
