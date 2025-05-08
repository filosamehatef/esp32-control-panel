<!DOCTYPE html>
<html>
<head>
  <title>SFA Control Panel (Firebase)</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <script src="https://www.gstatic.com/firebasejs/9.6.0/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.6.0/firebase-database-compat.js"></script>
<style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 20px;
      background-color: #f5f5f5;
    }
    .container {
      max-width: 600px;
      margin: 0 auto;
      background: white;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
    }
    h1 {
      text-align: center;
      color: #333;
    }
    .water-container {
      width: 100%;
      height: 40px;
      background: #e0e0e0;
      border-radius: 20px;
      margin: 20px 0;
      overflow: hidden;
      position: relative;
    }
    .water-fill {
      height: 100%;
      width: 0%;
      background: linear-gradient(to bottom, #1E90FF, #00BFFF);
      transition: width 0.5s;
      position: relative;
    }
    .water-wave {
      position: absolute;
      width: 200%;
      height: 200%;
      background: rgba(255,255,255,0.3);
      border-radius: 40%;
      animation: wave 5s linear infinite;
      transform: translate(-25%, -75%) rotate(0deg);
    }
    @keyframes wave {
      100% { transform: translate(-25%, -75%) rotate(360deg); }
    }
    .status-lights {
      display: flex;
      justify-content: space-around;
      margin: 20px 0;
    }
    .light {
      width: 60px;
      height: 60px;
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      font-weight: bold;
      color: white;
      box-shadow: 0 2px 5px rgba(0,0,0,0.2);
    }
    .motor1 { background-color: #FF0000; }
    .motor1.on { background-color: #00FF00; }
    .motor2 { background-color: #FF0000; }
    .motor2.on { background-color: #00FF00; }
    .horn { background-color: #FF0000; }
    .horn.alarm { animation: alarm 1s infinite; }
    @keyframes alarm {
      0%, 100% { background-color: #FF0000; }
      50% { background-color: #00FF00; }
    }
    .control-panel {
      margin: 20px 0;
    }
    .button-row {
      display: flex;
      margin-bottom: 15px;
    }
    .button {
      flex: 1;
      padding: 15px;
      margin: 0 5px;
      border: none;
      border-radius: 5px;
      color: white;
      font-size: 16px;
      font-weight: bold;
      cursor: pointer;
      transition: all 0.2s;
    }
    .button:disabled {
      opacity: 0.6;
      cursor: not-allowed;
      background-color: #cccccc !important;
    }
    .button:active:not(:disabled) {
      transform: translateY(2px);
    }
    .on { background-color: #4CAF50; }
    .off { background-color: #f44336; }
    .mode-btn {
      width: 100%;
      padding: 15px;
      margin-bottom: 15px;
      background-color: #2196F3;
      color: white;
      border: none;
      border-radius: 5px;
      font-size: 16px;
      font-weight: bold;
      cursor: pointer;
    }
    .mode-btn.active {
      background-color: #0b7dda;
      box-shadow: inset 0 0 5px rgba(0,0,0,0.2);
    }
    .mode-btn:disabled {
      background-color: #cccccc;
    }
    .control-mode-btn {
      width: 100%;
      padding: 15px;
      margin-bottom: 15px;
      background-color: #9C27B0;
      color: white;
      border: none;
      border-radius: 5px;
      font-size: 16px;
      font-weight: bold;
      cursor: pointer;
    }
    .control-mode-btn.active {
      background-color: #7B1FA2;
      box-shadow: inset 0 0 5px rgba(0,0,0,0.2);
    }
    .status {
      padding: 15px;
      background: #f9f9f9;
      border-radius: 5px;
      margin-top: 20px;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>SFA Control Panel</h1>

    <div class="water-container">
      <div class="water-fill" id="waterFill">
        <div class="water-wave"></div>
      </div>
    </div>

    <div class="status-lights">
      <div class="light motor1" id="motor1Light">M1</div>
      <div class="light motor2" id="motor2Light">M2</div>
      <div class="light horn" id="hornLight">H</div>
    </div>

    <div class="control-panel">
      <button class="control-mode-btn" id="mobileControlBtn" onclick="setControlMode('mobile')">Mobile Control</button>
      <button class="control-mode-btn" id="panelControlBtn" onclick="setControlMode('panel')">Panel Control</button>

      <button class="mode-btn" id="autoBtn" onclick="setMode('auto')">Automatic Mode</button>
      <button class="mode-btn" id="manualBtn" onclick="setMode('manual')">Manual Mode</button>

      <div class="button-row">
        <button class="button on" id="motor1On" onclick="controlMotor(1, 1)">Motor 1 ON</button>
        <button class="button off" id="motor1Off" onclick="controlMotor(1, 0)">Motor 1 OFF</button>
      </div>

      <div class="button-row">
        <button class="button on" id="motor2On" onclick="controlMotor(2, 1)">Motor 2 ON</button>
        <button class="button off" id="motor2Off" onclick="controlMotor(2, 0)">Motor 2 OFF</button>
      </div>
    </div>

    <div class="status" id="status">
      Control Mode: <span id="controlModeStatus">Loading...</span><br>
      Operation Mode: <span id="modeStatus">Loading...</span><br>
      Motor 1: <span id="motor1Status">Loading...</span><br>
      Motor 2: <span id="motor2Status">Loading...</span><br>
      Horn: <span id="hornStatus">Loading...</span><br>
      Water Level: <span id="waterLevelStatus">Loading...</span>
    </div>
  </div>

 <script>
    // Firebase Config
    const firebaseConfig = {
      apiKey: "AIzaSyB0RjvOZYVNqTJN6Ri709GMnvMP1QsG3fk",
      databaseURL: "https://espcontrol-58d06-default-rtdb.firebaseio.com/"
    };

    // Initialize Firebase
    const app = firebase.initializeApp(firebaseConfig);
    const database = firebase.database();
    
    // State variables
    let isMobile = false;
    let isManual = false;

    // Firebase Realtime Listener
    database.ref('/').on('value', (snapshot) => {
      const data = snapshot.val();
      if (data) {
        updateUI(data);
      }
    });

    // Update UI with Firebase data
    function updateUI(data) {
      // Update lights
      document.getElementById("motor1Light").className = 
        `light motor1 ${data.motor1 ? 'on' : ''}`;
      document.getElementById("motor2Light").className = 
        `light motor2 ${data.motor2 ? 'on' : ''}`;
      document.getElementById("hornLight").className = 
        `light horn ${data.emergency ? 'alarm' : ''}`;
      
      // Update water level
      document.getElementById("waterFill").style.width = 
        `${data.waterLevel * 25}%`;
      
      // Update status text
      document.getElementById("controlModeStatus").textContent =
        data.controlMode === 'mobile' ? 'Mobile Control' : 'Panel Control';
      document.getElementById("modeStatus").textContent =
        data.mode === 'manual' ? 'Manual' : 'Automatic';
      document.getElementById("motor1Status").textContent =
        data.motor1 ? 'ON' : 'OFF';
      document.getElementById("motor2Status").textContent =
        data.motor2 ? 'ON' : 'OFF';
      document.getElementById("hornStatus").textContent =
        data.emergency ? 'ACTIVE' : 'INACTIVE';
      document.getElementById("waterLevelStatus").textContent =
        `${data.waterLevel * 25}%`;
      
      // Update local state
      isMobile = data.controlMode === 'mobile';
      isManual = data.mode === 'manual';
      
      // Update button states
      document.getElementById('mobileControlBtn').className =
        isMobile ? 'control-mode-btn active' : 'control-mode-btn';
      document.getElementById('panelControlBtn').className =
        isMobile ? 'control-mode-btn' : 'control-mode-btn active';
      document.getElementById('autoBtn').className =
        isManual ? 'mode-btn' : 'mode-btn active';
      document.getElementById('manualBtn').className =
        isManual ? 'mode-btn active' : 'mode-btn';
      
      // Enable/disable buttons
      const buttonsDisabled = !isMobile;
      document.getElementById('autoBtn').disabled = buttonsDisabled;
      document.getElementById('manualBtn').disabled = buttonsDisabled;
      document.getElementById('motor1On').disabled = buttonsDisabled || !isManual;
      document.getElementById('motor1Off').disabled = buttonsDisabled || !isManual;
      document.getElementById('motor2On').disabled = buttonsDisabled || !isManual;
      document.getElementById('motor2Off').disabled = buttonsDisabled || !isManual;
    }

    // Send commands to Firebase
    function controlMotor(motor, state) {
      database.ref(`/commands/motor${motor}`).set(state === 1);
    }

    function setMode(mode) {
      database.ref('/commands/mode').set(mode);
    }

    function setControlMode(mode) {
      database.ref('/commands/controlMode').set(mode);
    }
  </script>
</body>
</html>
