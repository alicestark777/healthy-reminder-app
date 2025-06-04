<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Healthy Reminder</title>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600&display=swap" rel="stylesheet">
  <style>
    * {
      box-sizing: border-box;
    }
    body {
      font-family: 'Inter', sans-serif;
      background: radial-gradient(circle at top left, #d0f0fd, #fef9e7);
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      min-height: 100vh;
      margin: 0;
      padding: 2rem;
    }
    .container, .reminder-container {
      background-color: #ffffffee;
      padding: 2.5rem 3rem;
      border-radius: 20px;
      box-shadow: 0 15px 25px rgba(0, 0, 0, 0.15);
      text-align: center;
      max-width: 500px;
      width: 100%;
      backdrop-filter: blur(10px);
      animation: fadeIn 1s ease-in-out;
      margin-bottom: 2rem;
    }
    .hidden {
      display: none;
    }
    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(-20px); }
      to { opacity: 1; transform: translateY(0); }
    }
    h1 {
      color: #00796b;
      margin-bottom: 1rem;
      font-size: 2.5rem;
    }
    label {
      font-weight: 600;
      display: block;
      margin-top: 1.2rem;
      text-align: left;
      color: #333;
    }
    input[type="text"],
    input[type="time"] {
      width: 100%;
      padding: 0.9rem;
      margin-top: 0.3rem;
      border: 1px solid #ccc;
      border-radius: 12px;
      font-size: 1rem;
      background: #f9f9f9;
      transition: border-color 0.3s;
    }
    input:focus {
      border-color: #00796b;
      outline: none;
    }
    button {
      background-color: #00796b;
      color: white;
      padding: 0.9rem 2rem;
      border: none;
      border-radius: 12px;
      font-size: 1rem;
      margin-top: 2rem;
      cursor: pointer;
      transition: background-color 0.3s ease, transform 0.2s ease;
    }
    button:hover {
      background-color: #004d40;
      transform: scale(1.03);
    }
    .reminder-list {
      margin-top: 2.5rem;
      text-align: left;
    }
    .reminder-item {
      background: #f0fff0;
      border: 2px solid #a5d6a7;
      border-left: 8px solid #43a047;
      border-radius: 14px;
      padding: 1.2rem 1.4rem;
      margin-bottom: 1.2rem;
      position: relative;
      box-shadow: 0 2px 10px rgba(0, 0, 0, 0.06);
    }
    .delete-btn {
      position: absolute;
      top: 10px;
      right: 10px;
      background-color: #f44336;
      color: white;
      border: none;
      padding: 6px 12px;
      border-radius: 8px;
      font-size: 0.85rem;
      cursor: pointer;
      transition: background-color 0.3s ease;
    }
    .delete-btn:hover {
      background-color: #c62828;
    }
    .timer {
      font-weight: bold;
      margin-top: 0.8rem;
      color: #1b5e20;
    }
  </style>
</head>
<body>
  <div class="container" id="registerContainer">
    <h1>💊 Healthy Reminder</h1>
    <label for="username">Register Username:</label>
    <input type="text" id="username" placeholder="e.g., user123" />
    <button onclick="registerUser()">Register</button>
  </div>

  <div class="reminder-container hidden" id="reminderContainer">
    <h1 id="greeting"></h1>
    <label for="medName">Medicine Name:</label>
    <input type="text" id="medName" placeholder="e.g., Vitamin D" />

    <label for="medTime">Reminder Time:</label>
    <input type="time" id="medTime" />

    <button onclick="setReminder()">Add Reminder</button>
    <div class="reminder-list" id="reminderList"></div>
  </div>

  <script>
    let currentUser = "";

    function registerUser() {
      const usernameInput = document.getElementById("username");
      const username = usernameInput.value.trim();
      if (!username) {
        alert("Please enter a username.");
        return;
      }
      currentUser = username;
      document.getElementById("greeting").innerText = `👋 Welcome, ${currentUser}!`;
      document.getElementById("registerContainer").classList.add("hidden");
      document.getElementById("reminderContainer").classList.remove("hidden");
    }

    function setReminder() {
      const name = document.getElementById("medName").value;
      const time = document.getElementById("medTime").value;
      const list = document.getElementById("reminderList");

      if (!name || !time) {
        alert("Please fill in both fields.");
        return;
      }

      const reminderItem = document.createElement("div");
      reminderItem.className = "reminder-item";

      const content = document.createElement("div");
      content.innerHTML = `👤 <strong>${currentUser}</strong> → ⏰ <strong>${name}</strong> at <strong>${time}</strong>`;

      const timer = document.createElement("div");
      timer.className = "timer";
      updateCountdown(timer, time);
      const interval = setInterval(() => updateCountdown(timer, time), 1000);

      const deleteBtn = document.createElement("button");
      deleteBtn.textContent = "Delete";
      deleteBtn.className = "delete-btn";
      deleteBtn.onclick = () => {
        clearInterval(interval);
        reminderItem.remove();
      };

      reminderItem.appendChild(content);
      reminderItem.appendChild(timer);
      reminderItem.appendChild(deleteBtn);
      list.appendChild(reminderItem);

      const [hour, minute] = time.split(":" ).map(Number);
      const now = new Date();
      const target = new Date();
      target.setHours(hour, minute, 0);
      const delay = target - now;
      if (delay > 0) {
        setTimeout(() => alert(`💊 ${currentUser}, it's time to take your medication: ${name}`), delay);
      }

      document.getElementById("medName").value = "";
      document.getElementById("medTime").value = "";
    }

    function updateCountdown(timerElem, timeStr) {
      const [hour, minute] = timeStr.split(":" ).map(Number);
      const now = new Date();
      const target = new Date();
      target.setHours(hour, minute, 0);

      const diff = target - now;
      if (diff <= 0) {
        timerElem.textContent = "⏳ It's time!";
      } else {
        const mins = Math.floor(diff / 60000);
        const secs = Math.floor((diff % 60000) / 1000);
        timerElem.textContent = `Countdown: ${mins}m ${secs}s`;
      }
    }
  </script>
</body>
</html>

