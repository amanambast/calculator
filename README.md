# calculator<!DOCTYPE html>
<html lang="hi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>मल्टीटास्किंग कैलकुलेटर</title>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        body {
            background: linear-gradient(135deg, #74b9ff, #0984e3);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }
        .container {
            background: #fff;
            width: 100%;
            max-width: 400px;
            border-radius: 12px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.2);
            overflow: hidden;
        }
        .tabs {
            display: flex;
            background: #dfe6e9;
        }
        .tab-btn {
            flex: 1;
            padding: 12px 0;
            border: none;
            background: none;
            cursor: pointer;
            font-weight: bold;
            color: #636e72;
            transition: 0.3s;
            font-size: 14px;
        }
        .tab-btn.active {
            background: #fff;
            color: #0984e3;
            border-bottom: 3px solid #0984e3;
        }
        .tool-content {
            display: none;
            padding: 20px;
        }
        .tool-content.active {
            display: block;
        }
        /* Calculator Style */
        #calc-display {
            width: 100%;
            height: 50px;
            background: #2d3436;
            color: #fff;
            font-size: 24px;
            text-align: right;
            padding: 10px;
            border: none;
            border-radius: 6px;
            margin-bottom: 15px;
        }
        .calc-keys {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 10px;
        }
        .calc-keys button {
            padding: 15px;
            font-size: 18px;
            border: none;
            background: #f1f2f6;
            cursor: pointer;
            border-radius: 6px;
            transition: 0.2s;
        }
        .calc-keys button:hover {
            background: #dfe6e9;
        }
        .calc-keys button.operator {
            background: #ffeaa7;
            color: #d63031;
            font-weight: bold;
        }
        .calc-keys button.equal {
            background: #00b894;
            color: white;
            grid-column: span 2;
        }
        /* Converter & Age Style */
        .input-group {
            margin-bottom: 15px;
        }
        .input-group label {
            display: block;
            margin-bottom: 5px;
            color: #2d3436;
            font-weight: 600;
        }
        .input-group input, .input-group select {
            width: 100%;
            padding: 10px;
            border: 1px solid #b2bec3;
            border-radius: 6px;
            font-size: 16px;
        }
        .result-box {
            margin-top: 15px;
            padding: 12px;
            background: #e8f4f8;
            border-radius: 6px;
            color: #0984e3;
            font-weight: bold;
            text-align: center;
            font-size: 18px;
        }
        .action-btn {
            width: 100%;
            padding: 12px;
            background: #0984e3;
            color: white;
            border: none;
            border-radius: 6px;
            font-size: 16px;
            cursor: pointer;
            font-weight: bold;
        }
        .action-btn:hover {
            background: #74b9ff;
        }
    </style>
</head>
<body>

<div class="container">
    <!-- Navigation Tabs -->
    <div class="tabs">
        <button class="tab-btn active" onclick="switchTab(event, 'calculator')">कैलकुलेटर</button>
        <button class="tab-btn active" onclick="switchTab(event, 'converter')" style="background: none;">यूनिट कनवर्टर</button>
        <button class="tab-btn" onclick="switchTab(event, 'ageCalc')">आयु कैलकुलेटर</button>
    </div>

    <!-- 1. Standard Calculator -->
    <div id="calculator" class="tool-content active">
        <input type="text" id="calc-display" readonly>
        <div class="calc-keys">
            <button onclick="clearCalc()">C</button>
            <button onclick="appendCalc('%')">%</button>
            <button onclick="appendCalc('/')" class="operator">÷</button>
            <button onclick="appendCalc('*')" class="operator">×</button>
            <button onclick="appendCalc('7')">7</button>
            <button onclick="appendCalc('8')">8</button>
            <button onclick="appendCalc('9')">9</button>
            <button onclick="appendCalc('-')" class="operator">-</button>
            <button onclick="appendCalc('4')">4</button>
            <button onclick="appendCalc('5')">5</button>
            <button onclick="appendCalc('6')">6</button>
            <button onclick="appendCalc('+')" class="operator">+</button>
            <button onclick="appendCalc('1')">1</button>
            <button onclick="appendCalc('2')">2</button>
            <button onclick="appendCalc('3')">3</button>
            <button onclick="calculateResult()" class="equal">=</button>
            <button onclick="appendCalc('0')">0</button>
            <button onclick="appendCalc('.')">.</button>
        </div>
    </div>

    <!-- 2. Unit Converter -->
    <div id="converter" class="tool-content">
        <div class="input-group">
            <label>प्रकार चुनें:</label>
            <select id="con-type" onchange="updateUnits()">
                <option value="length">लंबाई (मीटर / किलोमीटर)</option>
                <option value="weight">वजन (ग्राम / किलोग्राम)</option>
            </select>
        </div>
        <div class="input-group">
            <label>मान दर्ज करें:</label>
            <input type="number" id="con-value" placeholder="संख्या लिखें...">
        </div>
        <div class="input-group">
            <label>बदलाव:</label>
            <select id="con-rule">
                <option value="m_to_km">मीटर से किलोमीटर</option>
                <option value="km_to_m">किलोमीटर से मीटर</option>
            </select>
        </div>
        <button class="action-btn" onclick="convertUnit()">बदलें</button>
        <div class="result-box" id="con-result">परिणाम: --</div>
    </div>

    <!-- 3. Age Calculator -->
    <div id="ageCalc" class="tool-content">
        <div class="input-group">
            <label>अपनी जन्मतिथि चुनें:</label>
            <input type="date" id="birth-date">
        </div>
        <button class="action-btn" onclick="calculateAge()">आयु निकालें</button>
        <div class="result-box" id="age-result">आयु: --</div>
    </div>
</div>

<script>
    // Tab Switching Logic
    function switchTab(evt, toolName) {
        let contents = document.getElementsByClassName("tool-content");
        for (let c of contents) c.classList.remove("active");
        
        let buttons = document.getElementsByClassName("tab-btn");
        for (let b of buttons) b.classList.remove("active");
        
        document.getElementById(toolName).classList.add("active");
        evt.currentTarget.classList.add("active");
    }

    // Standard Calculator Logic
    let display = document.getElementById("calc-display");
    function appendCalc(val) {
        display.value += val;
    }
    function clearCalc() {
        display.value = "";
    }
    function calculateResult() {
        try {
            display.value = eval(display.value);
        } catch (e) {
            display.value = "अमान्य (Error)";
        }
    }

    // Unit Converter Logic
    function updateUnits() {
        let type = document.getElementById("con-type").value;
        let rule = document.getElementById("con-rule");
        rule.innerHTML = "";
        if (type === "length") {
            rule.innerHTML = `<option value="m_to_km">मीटर से किलोमीटर</option><option value="km_to_m">किलोमीटर से मीटर</option>`;
        } else {
            rule.innerHTML = `<option value="g_to_kg">ग्राम से किलोग्राम</option><option value="kg_to_g">किलोग्राम से ग्राम</option>`;
        }
    }

    function convertUnit() {
        let val = parseFloat(document.getElementById("con-value").value);
        let rule = document.getElementById("con-rule").value;
        let resBox = document.getElementById("con-result");

        if (isNaN(val)) {
            resBox.innerHTML = "कृपया सही मान दर्ज करें!";
            return;
        }

        let ans = 0;
        if (rule === "m_to_km") ans = val / 1000 + " किमी";
        else if (rule === "km_to_m") ans = val * 1000 + " मीटर";
        else if (rule === "g_to_kg") ans = val / 1000 + " किग्रा";
        else if (rule === "kg_to_g") ans = val * 1000 + " ग्राम";

        resBox.innerHTML = "परिणाम: " + ans;
    }

    // Age Calculator Logic
    function calculateAge() {
        let bDate = new Date(document.getElementById("birth-date").value);
        let today = new Date();
        let resBox = document.getElementById("age-result");

        if (isNaN(bDate.getTime())) {
            resBox.innerHTML = "कृपया जन्मतिथि चुनें!";
            return;
        }

        let years = today.getFullYear() - bDate.getFullYear();
        let months = today.getMonth() - bDate.getMonth();
        let days = today.getDate() - bDate.getDate();

        if (days < 0) {
            months--;
            days += new Date(today.getFullYear(), today.getMonth(), 0).getDate();
        }
        if (months < 0) {
            years--;
            months += 12;
        }

        resBox.innerHTML = `${years} वर्ष, ${months} महीने, ${days} दिन`;
    }
</script>

</body>
</html>
