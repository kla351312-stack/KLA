<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Roblox Luau Minifier & Loadstring Generator</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #1e1e2e;
            color: #cdd6f4;
            margin: 0;
            padding: 40px 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            box-sizing: border-box;
        }
        .container {
            width: 100%;
            max-width: 1000px;
            background-color: #252538;
            padding: 30px;
            border-radius: 12px;
            box-shadow: 0 8px 24px rgba(0, 0, 0, 0.3);
        }
        h1 {
            text-align: center;
            color: #89b4fa;
            margin-top: 0;
            margin-bottom: 5px;
        }
        p.subtitle {
            text-align: center;
            color: #a6adc8;
            margin-bottom: 25px;
            font-size: 14px;
        }
        .textarea-group {
            display: grid;
            grid-template-columns: 1fr;
            gap: 20px;
            margin-bottom: 20px;
        }
        @media (min-width: 768px) {
            .textarea-group {
                grid-template-columns: 1fr 1fr;
            }
        }
        label {
            display: block;
            margin-bottom: 8px;
            font-weight: bold;
            color: #bac2de;
        }
        textarea {
            width: 100%;
            height: 380px;
            background-color: #181825;
            color: #a6e3a1;
            border: 1px solid #45475a;
            border-radius: 6px;
            padding: 12px;
            font-family: 'Courier New', Courier, monospace;
            font-size: 13px;
            resize: none;
            box-sizing: border-box;
        }
        textarea:focus {
            outline: none;
            border-color: #89b4fa;
        }
        .loadstring-input {
            width: 100%;
            padding: 10px;
            background-color: #181825;
            border: 1px solid #45475a;
            border-radius: 6px;
            color: #f9e2af;
            font-family: monospace;
            margin-bottom: 20px;
            box-sizing: border-box;
        }
        .loadstring-input:focus {
            outline: none;
            border-color: #f9e2af;
        }
        .btn-group {
            display: flex;
            gap: 10px;
            justify-content: center;
            flex-wrap: wrap;
        }
        button {
            background-color: #89b4fa;
            color: #11111b;
            border: none;
            padding: 12px 20px;
            font-size: 15px;
            font-weight: bold;
            border-radius: 6px;
            cursor: pointer;
            transition: background-color 0.2s;
        }
        button:hover {
            background-color: #b4befe;
        }
        .btn-loadstring {
            background-color: #fab387;
        }
        .btn-loadstring:hover {
            background-color: #f9e2af;
        }
        .btn-download {
            background-color: #a6e3a1;
        }
        .btn-download:hover {
            background-color: #94e2d5;
        }
        .btn-clear {
            background-color: #f38ba8;
        }
        .btn-clear:hover {
            background-color: #eba0b3;
        }
        .stats {
            margin-top: 10px;
            font-size: 13px;
            color: #a6adc8;
            text-align: right;
        }
    </style>
</head>
<body>

<div class="container">
    <h1>Roblox Script Minifier ⚡</h1>
    <p class="subtitle">ย่อขนาดโค้ด Luau และแปลงเป็นสคริปต์ลัดรูปแบบ Loadstring</p>
    
    <div>
        <label for="rawUrl">ลิงก์โค้ดดิบของคุณ (จาก dpaste, pastebin หรือ github):</label>
        <input type="text" id="rawUrl" class="loadstring-input" placeholder="ตัวอย่าง: https://dpaste.com/GHTMUD2ZP.txt">
    </div>

    <div class="textarea-group">
        <div>
            <label for="inputScript">สคริปต์ต้นฉบับ (Original Luau โค้ดเต็ม):</label>
            <textarea id="inputScript" placeholder="วางสคริปต์เต็มของคุณที่นี่ (เช่น สคริปต์มองทะลุ/ล็อคหัว)..."></textarea>
        </div>
        <div>
            <label for="outputScript">ผลลัพธ์ (Minified / Loadstring Script):</label>
            <textarea id="outputScript" placeholder="ผลลัพธ์การย่อหรือสคริปต์ลัดจะแสดงตรงนี้..." readonly></textarea>
            <div class="stats" id="stats">ลดขนาดไปได้: 0%</div>
        </div>
    </div>

    <div class="btn-group">
        <button class="btn-loadstring" onclick="generateLoadstring()">🔗 สร้างเป็น Loadstring</button>
        <button onclick="minifyLuauScript()">⚡ ย่อสคริปต์ดิบ</button>
        <button class="btn-download" onclick="downloadScript()">💾 ดาวน์โหลดไฟล์ .txt</button>
        <button onclick="copyToClipboard()">📋 คัดลอกโค้ด</button>
        <button class="btn-clear" onclick="clearAll()">🧹 ล้างทั้งหมด</button>
    </div>
</div>

<script>
    // ฟังก์ชันย่อสคริปต์สำหรับ Roblox Luau โดนเฉพาะ
    function minifyLuauScript() {
        const input = document.getElementById('inputScript').value;
        if (!input.trim()) {
            alert('กรุณาใส่สคริปต์ในช่องซ้ายมือก่อนครับ');
            return;
        }

        let minified = input;

        // 1. ลบ Comment แบบหลายบรรทัด --[[ ... ]]
        minified = minified.replace(/--\[\[[\s\S]*?\]\]/g, '');

        // 2. ลบ Comment แบบบรรทัดเดียว -- ...
        minified = minified.replace(/--.*\$/gm, '');

        // 3. ยุบช่องว่างซ้ำๆ และการขึ้นบรรทัดใหม่
        minified = minified.replace(/\s+/g, ' ');
        minified = minified.trim();

        document.getElementById('outputScript').value = minified;
        calculateStats(input.length, minified.length);
    }

    // ฟังก์ชันสร้างสคริปต์ย่อแบบ loadstring(game:HttpGet(...))()
    function generateLoadstring() {
        const urlInput = document.getElementById('rawUrl').value.trim();
        
        if (!urlInput) {
            alert('กรุณาใส่ลิงก์โค้ดดิบของคุณในช่องด้านบนก่อนครับ เช่น https://dpaste.com');
            return;
        }

        // แปลงให้อยู่ในรูปแบบที่ต้องการ
        const loadstringTemplate = `loadstring(game:HttpGet("${urlInput}"))()`;
        
        document.getElementById('outputScript').value = loadstringTemplate;
        document.getElementById('stats').innerText = 'สร้างสคริปต์ลัดสำเร็จ!';
    }

    function calculateStats(originalLen, minifiedLen) {
        if (originalLen === 0) return;
        const saved = originalLen - minifiedLen;
        const percent = ((saved / originalLen) * 100).toFixed(1);
        document.getElementById('stats').innerText = `ลดขนาดไปได้: ${percent}% (ประหยัดได้ ${saved} ตัวอักษร)`;
    }

    function copyToClipboard() {
        const output = document.getElementById('outputScript');
        if (!output.value) {
            alert('ไม่มีโค้ดให้คัดลอกครับ กรุณากดปุ่มย่อหรือสร้างสคริปต์ก่อน');
            return;
        }
        output.select();
        document.execCommand('copy');
        alert('คัดลอกสคริปต์ไปยัง Clipboard เรียบร้อยแล้ว!');
    }

    function downloadScript() {
        const outputText = document.getElementById('outputScript').value;
        if (!outputText) {
            alert('ไม่มีโค้ดให้ดาวน์โหลดครับ');
            return;
        }
        const blob = new Blob([outputText], { type: "text/plain;charset=utf-8" });
        const link = document.createElement("a");
        link.href = URL.createObjectURL(blob);
        link.download = "script.txt";
        link.click();
        URL.revokeObjectURL(link.href);
    }

    function clearAll() {
        document.getElementById('rawUrl').value = '';
        document.getElementById('inputScript').value = '';
        document.getElementById('outputScript').value = '';
        document.getElementById('stats').innerText = 'ลดขนาดไปได้: 0%';
    }
</script>

</body>
</html>
