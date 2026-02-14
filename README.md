<!DOCTYPE html>
<html lang="he" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>נבון AI - עוזר חכם</title>
    <link href="https://fonts.googleapis.com/css2?family=Heebo:wght@300;400;500;700;900&family=Rubik:wght@400;700&display=swap" rel="stylesheet">
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body {
            font-family: 'Heebo', sans-serif;
            background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
            min-height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 1rem;
        }
        .container {
            max-width: 800px;
            width: 100%;
            background: white;
            border-radius: 20px;
            box-shadow: 0 20px 60px rgba(0,0,0,0.1);
            overflow: hidden;
        }
        .header {
            background: linear-gradient(135deg, #0A2F51, #137547);
            color: white;
            padding: 2rem;
            text-align: center;
        }
        .header h1 {
            font-family: 'Rubik', sans-serif;
            font-size: 2.5rem;
            margin-bottom: 0.5rem;
        }
        .header p {
            opacity: 0.9;
            font-size: 1.1rem;
        }
        .chat-box {
            height: 500px;
            overflow-y: auto;
            padding: 2rem;
            background: #fafafa;
        }
        .message {
            margin-bottom: 1.5rem;
            display: flex;
            gap: 1rem;
        }
        .message.bot { flex-direction: row; }
        .message.user { flex-direction: row-reverse; }
        .avatar {
            width: 40px;
            height: 40px;
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.5rem;
            flex-shrink: 0;
        }
        .message.bot .avatar { background: linear-gradient(135deg, #137547, #F39237); }
        .message.user .avatar { background: linear-gradient(135deg, #0A2F51, #0A4F81); }
        .bubble {
            max-width: 70%;
            padding: 1rem 1.5rem;
            border-radius: 15px;
            line-height: 1.6;
        }
        .message.bot .bubble {
            background: white;
            border: 1px solid #e0e0e0;
        }
        .message.user .bubble {
            background: #0A2F51;
            color: white;
        }
        .input-area {
            padding: 1.5rem;
            background: white;
            border-top: 1px solid #e0e0e0;
            display: flex;
            gap: 1rem;
        }
        input {
            flex: 1;
            padding: 1rem;
            border: 2px solid #e0e0e0;
            border-radius: 10px;
            font-family: 'Heebo', sans-serif;
            font-size: 1rem;
        }
        input:focus {
            outline: none;
            border-color: #0A2F51;
        }
        button {
            padding: 1rem 2rem;
            background: linear-gradient(135deg, #0A2F51, #137547);
            color: white;
            border: none;
            border-radius: 10px;
            font-family: 'Heebo', sans-serif;
            font-weight: 600;
            cursor: pointer;
            font-size: 1rem;
        }
        button:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(10, 47, 81, 0.3);
        }
        .quick-btns {
            padding: 0 2rem 1.5rem;
            display: flex;
            flex-wrap: wrap;
            gap: 0.5rem;
        }
        .quick-btn {
            padding: 0.5rem 1rem;
            background: #f0f0f0;
            border: 1px solid #e0e0e0;
            border-radius: 20px;
            font-size: 0.9rem;
            cursor: pointer;
        }
        .quick-btn:hover {
            background: #0A2F51;
            color: white;
            border-color: #0A2F51;
        }
        @media (max-width: 768px) {
            .header h1 { font-size: 1.8rem; }
            .chat-box { height: 400px; padding: 1rem; }
            .bubble { max-width: 85%; }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🤖 נבון AI</h1>
            <p>עוזר חכם לנבון פרויקטים בחינוך</p>
        </div>
        
        <div class="chat-box" id="chatBox">
            <div class="message bot">
                <div class="avatar">🤖</div>
                <div class="bubble">
                    שלום! 👋 אני העוזר הווירטואלי של נבון פרויקטים בחינוך.<br><br>
                    אני יכול לעזור לך עם:<br>
                    📚 מידע על התוכניות שלנו<br>
                    🎨 סדנאות ופעילויות<br>
                    📝 הרשמה לצהרונים וקייטנות<br>
                    📞 פרטי יצירת קשר<br><br>
                    מה תרצה לדעת?
                </div>
            </div>
        </div>

        <div class="quick-btns">
            <button class="quick-btn" onclick="ask('מה שעות הפעילות?')">שעות פעילות</button>
            <button class="quick-btn" onclick="ask('איך ליצור קשר?')">יצירת קשר</button>
            <button class="quick-btn" onclick="ask('ספר על תוכניות הטכנולוגיה')">טכנולוגיה</button>
            <button class="quick-btn" onclick="ask('מה כולל תכנית ניצנים?')">תכנית ניצנים</button>
        </div>
        
        <div class="input-area">
            <input type="text" id="userInput" placeholder="הקלד/י את השאלה שלך..." onkeypress="if(event.key==='Enter') send()">
            <button onclick="send()">שלח</button>
        </div>
    </div>

    <script>
        const kb = {
            "אודות": "נבון פרויקטים בחינוך היא חברה מובילה בתחום התוכניות החינוכיות למוסדות חינוך בכל הארץ. אנחנו מספקים תוכניות העשרה, צהרונים, קייטנות וסדנאות במגוון תחומים.",
            "טכנולוגיה": "אנחנו מובילים בתחום הטכנולוגיה! מציעים תל\"ן למוסדות החינוך תוך שימוש ביישומי מחשב, תוכנות גרפיקה ועיצוב, ומתחמי מציאות מדומה בקייטנות.",
            "מדע": "מובילים במדע! הילדים נחשפים לתחומי מדע שונים - אסטרונומיה, פיזיקה, מתמטיקה, טיסנות, תעופה, כימיה, ביולוגיה ועוד.",
            "ניצנים": "תכנית ניצנים - תוכנית הצהרונים והקייטנות שלנו. כוללת מגוון פעילויות העשרה איכותיות.",
            "צהרון": "אנחנו מפעילים צהרונים איכותיים במסגרת תכנית ניצנים. ההרשמה מתבצעת דרך האתר שלנו.",
            "קייטנה": "מפעילים קייטנות בחופשות עם תוכניות העשרה מגוונות - טכנולוגיה, מדע, אומנות ועוד!",
            "שעות": "שעות הפעילות שלנו: ימים א'-ה' בין 09:00-15:30.",
            "קשר": "ניתן ליצור קשר: טלפון 08-6694007, ווטסאפ 08-6858276, מייל office@navon-p.co.il. כתובת: המלך חסן השני 1, קריית עקרון.",
            "טלפון": "טלפון המשרד: 08-6694007 או ווטסאפ: 08-6858276",
            "וואטסאפ": "ניתן ליצור קשר בווטסאפ במספר 08-6858276",
        };

        function addMsg(text, isUser) {
            const box = document.getElementById('chatBox');
            const msg = document.createElement('div');
            msg.className = `message ${isUser ? 'user' : 'bot'}`;
            msg.innerHTML = `
                <div class="avatar">${isUser ? '👤' : '🤖'}</div>
                <div class="bubble">${text}</div>
            `;
            box.appendChild(msg);
            box.scrollTop = box.scrollHeight;
        }

        function getReply(txt) {
            const lower = txt.toLowerCase();
            for (const [key, val] of Object.entries(kb)) {
                if (lower.includes(key.toLowerCase())) return val;
            }
            if (lower.includes('שע') || lower.includes('פתוח')) return kb["שעות"];
            if (lower.includes('מחיר')) return "מומלץ ליצור קשר למשרד בטלפון 08-6694007 לקבלת הצעת מחיר מותאמת אישית.";
            if (lower.includes('שלום') || lower.includes('היי')) return "שלום! שמח לעזור 😊 על מה תרצה לשמוע?";
            return "אני כאן לעזור! תוכל לשאול על התוכניות שלנו, שעות פעילות, הרשמה או פרטי קשר. מה מעניין אותך?";
        }

        function send() {
            const input = document.getElementById('userInput');
            const txt = input.value.trim();
            if (!txt) return;
            addMsg(txt, true);
            input.value = '';
            setTimeout(() => addMsg(getReply(txt), false), 500);
        }

        function ask(q) {
            document.getElementById('userInput').value = q;
            send();
        }
    </script>
</body>
</html>
