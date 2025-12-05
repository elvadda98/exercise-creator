<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Auto Vocabulary Exercise Generator</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet"
          href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">

    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }

        body {
            font-family: 'Segoe UI', Tahoma, sans-serif;
            background: linear-gradient(135deg, #009688 0%, #4CAF50 100%);
            min-height: 100vh;
            padding: 20px;
            color: #2c3e50;
            display: flex;
            justify-content: center;
        }

        .container {
            max-width: 1100px;
            width: 100%;
            background: #fff;
            border-radius: 18px;
            box-shadow: 0 12px 30px rgba(0,0,0,0.25);
            overflow: hidden;
        }

        .header {
            background: linear-gradient(135deg, #00695c, #009688);
            color: white;
            padding: 25px;
            text-align: center;
        }

        .score {
            position: fixed;
            top: 20px;
            right: 20px;
            background: #00796b;
            color: white;
            padding: 10px 18px;
            border-radius: 25px;
            font-weight: bold;
            box-shadow: 0 4px 10px rgba(0,0,0,0.25);
            z-index: 1000;
        }

        .setup-panel {
            padding: 20px;
            background: #f1f8e9;
            border-bottom: 2px solid #c5e1a5;
        }

        .setup-panel h2 {
            margin-bottom: 8px;
            color: #33691e;
        }

        #vocab-input {
            width: 100%;
            min-height: 120px;
            padding: 10px;
            border-radius: 10px;
            border: 1px solid #b0bec5;
            font-family: inherit;
            margin-bottom: 10px;
        }

        .generate-btn {
            padding: 10px 20px;
            border-radius: 22px;
            border: none;
            cursor: pointer;
            font-weight: bold;
            background: #00796b;
            color: white;
        }

        .nav-buttons {
            display: flex;
            justify-content: center;
            flex-wrap: wrap;
            gap: 10px;
            background: #f8f9fa;
            padding: 15px 20px;
        }

        .nav-btn {
            padding: 10px 22px;
            border-radius: 22px;
            border: none;
            cursor: pointer;
            font-weight: 600;
            background: #ffffff;
            color: #444;
            transition: all .2s ease;
        }

        .nav-btn.active {
            background: #009688;
            color: white;
            transform: translateY(-2px);
        }

        .game-section {
            display: none;
            padding: 25px 30px 30px;
            min-height: 320px;
        }

        .game-section.active { display: block; }

        .vocabulary-item {
            background: #f8f9fa;
            padding: 16px;
            border-radius: 10px;
            margin-bottom: 14px;
        }

        .vocabulary-item h3 { margin-bottom: 6px; }

        .vocabulary-item button {
            margin-left: 8px;
            padding: 4px 10px;
            border-radius: 15px;
            border: none;
            cursor: pointer;
            font-size: 13px;
            background: #ff9800;
            color: white;
        }

        .word-bank {
            background: #e0f2f1;
            padding: 18px;
            border-radius: 14px;
            border: 2px solid #009688;
            margin-bottom: 22px;
        }

        .word-option {
            display: inline-block;
            background: #009688;
            color: white;
            padding: 7px 12px;
            border-radius: 18px;
            font-weight: 600;
            margin: 3px 4px;
            font-size: 14px;
        }

        .question {
            background: #f8f9fa;
            padding: 16px;
            border-radius: 10px;
            margin-bottom: 14px;
            border-left: 4px solid #009688;
        }

        .fill-blank {
            border: 2px solid #ccc;
            padding: 6px 10px;
            border-radius: 8px;
            min-width: 130px;
        }

        .fill-blank-pron {
            border-bottom: 2px solid #00796b;
            font-weight: bold;
            min-width: 130px;
            display: inline-block;
            padding: 3px 2px;
        }

        .check-btn, .reset-btn {
            padding: 10px 20px;
            border-radius: 22px;
            border: none;
            cursor: pointer;
            margin-top: 10px;
            font-size: 15px;
        }

        .check-btn { background: #009688; color: white; }
        .reset-btn { background: #f44336; color: white; }

        .feedback {
            margin-top: 7px;
            padding: 9px;
            border-radius: 8px;
            display: none;
            font-weight: 600;
        }

        .feedback.correct { background: #e8f5e9; color: #2e7d32; }
        .feedback.incorrect { background: #ffebee; color: #c62828; }

        .match-container { display: flex; gap: 20px; }
        .match-col { width: 50%; }

        .match-item {
            background: #ffffff;
            padding: 11px;
            margin: 6px 0;
            border-radius: 10px;
            border: 2px solid #ddd;
            cursor: pointer;
        }

        .match-item.selected {
            background: #b2dfdb;
            border-color: #009688;
        }

        .match-item.matched {
            background: #4caf50;
            color: white;
            border-color: #2e7d32;
            cursor: default;
        }

        .pron-btn {
            padding: 6px 10px;
            background: #00796b;
            color: white;
            border-radius: 18px;
            border: none;
            cursor: pointer;
            margin-left: 6px;
        }

        @media (max-width: 768px) {
            .match-container { flex-direction: column; }
            .match-col { width: 100%; }
            .score { position: static; margin-bottom: 10px; text-align: center; }
        }
    </style>
</head>

<body>
<div class="score">Score: <span id="score">0</span>/<span id="score-max">0</span></div>

<div class="container">

    <div class="header">
        <h1>Auto Vocabulary Exercise Generator</h1>
        <p>Paste your words, click "Generate", and play.</p>
    </div>

    <!-- SETUP PANEL -->
    <div class="setup-panel">
        <h2>1. Paste your vocabulary list</h2>
        <p style="margin-bottom:6px; font-size:14px;">
            Format: <code>word (as context/Italian)</code> — one per line.<br>
            Examples:<br>
            <code>benefit (as company benefit)</code><br>
            <code>swamp (as palude)</code><br>
            <code>onwards (as da quel momento in poi)</code>
        </p>
        <textarea id="vocab-input"
                  placeholder="benefit (as company benefit)
add-ons (as cose che si aggiungono)
upfront investment (as investimento iniziale)
humble (as umile)
swamp (as palude)
onwards (as da quel momento in poi)"></textarea>
        <button class="generate-btn" onclick="generateExercise()">
            <i class="fas fa-wand-magic-sparkles"></i> Generate Exercise
        </button>
    </div>

    <!-- NAVIGATION -->
    <div class="nav-buttons">
        <button class="nav-btn active" onclick="showSection(this, 'vocabulary')">Vocabulary List</button>
        <button class="nav-btn" onclick="showSection(this, 'writing')">Fill in the Gaps</button>
        <button class="nav-btn" onclick="showSection(this, 'matching')">Match Definitions</button>
        <button class="nav-btn" onclick="showSection(this, 'pronunciation')">Fill in the Gaps (Pronunciation)</button>
    </div>

    <!-- SECTIONS -->
    <div id="vocabulary" class="game-section active">
        <h2 style="margin-bottom:15px;">Vocabulary List</h2>
        <p style="font-size:14px; margin-bottom:15px; color:#555;">
            After generating, your words will appear here with meanings and audio buttons.
        </p>
        <div id="vocab-list"></div>
    </div>

    <div id="writing" class="game-section">
        <div class="word-bank">
            <h3>Word Bank</h3>
            <div id="word-bank-writing"></div>
        </div>
        <div id="writing-container"></div>
        <button class="check-btn" onclick="checkWriting()">Check Answers</button>
        <button class="reset-btn" onclick="resetWriting()">Reset</button>
    </div>

    <div id="matching" class="game-section">
        <h2 style="margin-bottom:15px;">Match Definitions</h2>
        <div class="match-container">
            <div class="match-col">
                <h3>Words</h3>
                <div id="match-words"></div>
            </div>
            <div class="match-col">
                <h3>Definitions</h3>
                <div id="definitions-container"></div>
            </div>
        </div>
        <button class="reset-btn" onclick="resetMatching()">Reset</button>
    </div>

    <div id="pronunciation" class="game-section">
        <div class="word-bank">
            <h3>Word Bank</h3>
            <div id="word-bank-pron"></div>
        </div>
        <h2 style="margin-bottom:10px;">Fill in the Gaps (Pronunciation)</h2>
        <p style="font-size:14px; margin-bottom:15px; color:#555;">
            Click the microphone and say the missing word in English.
        </p>
        <div id="pron-container"></div>
    </div>

</div>

<script>
    /************* GLOBAL STATE *************/
    let vocabItems = [];          // {word, context, meaningEn, example, writeSentence, writeAnswer, pronSentence, pronAnswer}
    let activeWritingSentences = [];
    let activePronSentences = [];
    let pronFlags = [];

    let writingCorrect = 0;
    let matchingCorrect = 0;
    let pronCorrect = 0;

    let matched = [];
    let selectedWordEl = null;
    let selectedDefEl = null;

    let recognition = null;

    if ('webkitSpeechRecognition' in window) {
        recognition = new webkitSpeechRecognition();
    } else if ('SpeechRecognition' in window) {
        recognition = new SpeechRecognition();
    }
    if (recognition) {
        recognition.lang = "en-US";
        recognition.interimResults = false;
        recognition.maxAlternatives = 1;
    }

    /************* HELPERS *************/
    function shuffle(arr) {
        for (let i = arr.length - 1; i > 0; i--) {
            const j = Math.floor(Math.random() * (i + 1));
            [arr[i], arr[j]] = [arr[j], arr[i]];
        }
        return arr;
    }

    function normalizeAnswer(str) {
        return (str || "").toLowerCase().trim().replace(/[\s\-]+/g, "");
    }

    function updateScore() {
        const totalMax = vocabItems.length * 3;
        document.getElementById("score").textContent =
            writingCorrect + matchingCorrect + pronCorrect;
        document.getElementById("score-max").textContent = totalMax;
    }

    function speak(word) {
        const u = new SpeechSynthesisUtterance(word);
        u.lang = "en-US";
        speechSynthesis.speak(u);
    }

    /************* PARSER + GENERATOR *************/
    function parseVocabInput(raw) {
        const lines = raw.split(/\r?\n/).map(l => l.trim()).filter(l => l.length > 0);
        const items = [];

        const regex = /^(.+?)(?:\s*\((?:as\s*)?(.+?)\))?$/i;

        for (const line of lines) {
            const m = line.match(regex);
            if (!m) continue;
            const word = m[1].trim();
            const context = (m[2] || "").trim();
            if (!word) continue;
            items.push({ word, context });
        }
        return items;
    }

    function generateMeaningEn(word, context) {
        const baseTemplates = [
            (w, c) => `a word used when talking about ${c || "different situations"}`,
            (w, c) => `something related to ${c || "work or daily life"}`,
            (w, c) => `a term that you can hear in conversations about ${c || "many topics"}`,
            (w, c) => `an expression often used in context of ${c || "real-life situations"}`,
        ];
        const t = baseTemplates[Math.floor(Math.random() * baseTemplates.length)];
        return t(word, context);
    }

    function generateExampleSentence(word, context) {
        const w = word;
        const exTemplates = [
            () => `This ${w} is important in many situations.`,
            () => `People often talk about this ${w} at work.`,
            () => `I learned this ${w} during an English lesson.`,
            () => `Sometimes this ${w} can make a big difference.`,
            () => `You might hear this ${w} when people discuss ${context || "different topics"}.`
        ];
        const t = exTemplates[Math.floor(Math.random() * exTemplates.length)];
        return t();
    }

    function generateWritingSentence(word, context) {
        const w = word;
        const templates = [
            () => `This company offers a special ___ to its employees.`,
            () => `We need to think carefully about this ___.`,
            () => `Sometimes a small ___ can change everything.`,
            () => `They talked about the ___ during the meeting.`,
            () => `In this situation, that ___ is very useful.`,
            () => `The manager explained why the ___ was important.`
        ];
        const t = templates[Math.floor(Math.random() * templates.length)];
        return t();
    }

    function generatePronSentence(word, context) {
        const w = word;
        const templates = [
            () => `You will hear this ___ when people talk about ${context || "work"}.`,
            () => `The teacher asked the class to repeat the ___.`,
            () => `He used this ___ many times during his presentation.`,
            () => `They discussed this ___ in detail.`,
            () => `She didn't understand the ___ at first.`,
            () => `The boss mentioned this ___ in the email.`
        ];
        const t = templates[Math.floor(Math.random() * templates.length)];
        return t();
    }

    function buildVocabItems(parsed) {
        const items = parsed.map(p => {
            const meaningEn = generateMeaningEn(p.word, p.context);
            const example = generateExampleSentence(p.word, p.context);
            const writeSentence = generateWritingSentence(p.word, p.context);
            const pronSentence = generatePronSentence(p.word, p.context);

            return {
                word: p.word,
                context: p.context,
                meaningEn,
                example,
                writeSentence,
                writeAnswer: p.word,
                pronSentence,
                pronAnswer: p.word
            };
        });
        return items;
    }

    /************* BUILD UI *************/
    function generateExercise() {
        const raw = document.getElementById("vocab-input").value.trim();
        if (!raw) {
            alert("Please paste at least one vocabulary line.");
            return;
        }

        const parsed = parseVocabInput(raw);
        if (parsed.length === 0) {
            alert("No valid lines found. Use format: word (as context).");
            return;
        }

        vocabItems = buildVocabItems(parsed);

        buildVocabularySection();
        buildWordBanks();
        buildWritingSection();
        buildMatchingSection();
        buildPronunciationSection();

        writingCorrect = 0;
        matchingCorrect = 0;
        pronCorrect = 0;
        updateScore();

        showSection(document.querySelector('.nav-btn.active'), 'vocabulary');
    }

    function buildVocabularySection() {
        const list = document.getElementById("vocab-list");
        list.innerHTML = "";
        vocabItems.forEach(item => {
            const ctxLabel = item.context ? item.context : "—";
            list.innerHTML += `
                <div class="vocabulary-item">
                    <h3>${item.word}
                        <button onclick="speak('${item.word.replace(/'/g, "\\'")}')">
                            ▶️ Hear
                        </button>
                    </h3>
                    <p><strong>Context/Italian:</strong> ${ctxLabel}</p>
                    <p><strong>Simple meaning (EN):</strong> ${item.meaningEn}</p>
                    <p><em>Example:</em> ${item.example}</p>
                </div>
            `;
        });
    }

    function buildWordBanks() {
        const wbW = document.getElementById("word-bank-writing");
        const wbP = document.getElementById("word-bank-pron");
        wbW.innerHTML = "";
        wbP.innerHTML = "";
        vocabItems.forEach(item => {
            wbW.innerHTML += `<span class="word-option">${item.word}</span>`;
            wbP.innerHTML += `<span class="word-option">${item.word}</span>`;
        });
    }

    function buildWritingSection() {
        const cont = document.getElementById("writing-container");
        cont.innerHTML = "";

        activeWritingSentences = shuffle([...vocabItems]);

        activeWritingSentences.forEach((item, i) => {
            const htmlSentence = item.writeSentence.replace(
                "___",
                `<input class="fill-blank" data-answer="${item.writeAnswer.toLowerCase()}"
                        placeholder="answer">`
            );
            cont.innerHTML += `
                <div class="question">
                    <p>${htmlSentence}</p>
                    <div class="feedback" id="write-feed-${i}"></div>
                </div>
            `;
        });
    }

    function buildMatchingSection() {
        const wordsCol = document.getElementById("match-words");
        const defsCol = document.getElementById("definitions-container");
        wordsCol.innerHTML = "";
        defsCol.innerHTML = "";

        vocabItems.forEach(item => {
            wordsCol.innerHTML += `
                <div class="match-item" data-word="${item.word}"
                     onclick="selectMatchItem(this)">
                    ${item.word}
                </div>
            `;
        });

        const shuffledDefs = shuffle([...vocabItems]);
        shuffledDefs.forEach(item => {
            defsCol.innerHTML += `
                <div class="match-item" data-meaning="${item.word}"
                     onclick="selectMatchItem(this)">
                    ${item.meaningEn}
                </div>
            `;
        });

        matched = [];
        matchingCorrect = 0;
    }

    function buildPronunciationSection() {
        const cont = document.getElementById("pron-container");
        cont.innerHTML = "";

        activePronSentences = shuffle([...vocabItems]);
        pronFlags = activePronSentences.map(() => false);
        pronCorrect = 0;

        activePronSentences.forEach((item, i) => {
            const sentenceHtml = item.pronSentence.replace(
                "___",
                `<span id="pron-blank-${i}" class="fill-blank-pron">______</span>`
            );
            cont.innerHTML += `
                <div class="question">
                    <p>
                        ${sentenceHtml}
                        <button class="pron-btn" onclick="recordPronunciation(${i})">
                            <i class="fas fa-microphone"></i>
                        </button>
                    </p>
                    <div class="feedback" id="pron-feed-${i}"></div>
                </div>
            `;
        });
    }

    /************* WRITING LOGIC *************/
    function checkWriting() {
        if (!vocabItems.length) {
            alert("Generate an exercise first.");
            return;
        }

        const blanks = document.querySelectorAll("#writing .fill-blank");
        let correct = 0;

        activeWritingSentences.forEach((item, i) => {
            const input = blanks[i];
            const valNorm = normalizeAnswer(input.value);
            const ansNorm = normalizeAnswer(item.writeAnswer);
            const fb = document.getElementById(`write-feed-${i}`);

            if (valNorm === ansNorm && ansNorm !== "") {
                fb.textContent = "✔️ Correct!";
                fb.className = "feedback correct";
                fb.style.display = "block";
                correct++;
            } else {
                fb.textContent = `❌ Incorrect. Correct answer: "${item.writeAnswer}"`;
                fb.className = "feedback incorrect";
                fb.style.display = "block";
            }
        });

        writingCorrect = correct;
        updateScore();
    }

    function resetWriting() {
        if (!vocabItems.length) return;
        buildWritingSection();
        writingCorrect = 0;
        updateScore();
    }

    /************* MATCHING LOGIC *************/
    function selectMatchItem(el) {
        if (el.classList.contains("matched")) return;

        if (el.dataset.word) {
            if (selectedWordEl) selectedWordEl.classList.remove("selected");
            selectedWordEl = el;
            el.classList.add("selected");
        } else {
            if (selectedDefEl) selectedDefEl.classList.remove("selected");
            selectedDefEl = el;
            el.classList.add("selected");
        }

        if (selectedWordEl && selectedDefEl) {
            const w = selectedWordEl.dataset.word;
            const m = selectedDefEl.dataset.meaning;

            if (w === m) {
                selectedWordEl.classList.remove("selected");
                selectedDefEl.classList.remove("selected");
                selectedWordEl.classList.add("matched");
                selectedDefEl.classList.add("matched");
                matched.push(w);
            } else {
                selectedWordEl.classList.remove("selected");
                selectedDefEl.classList.remove("selected");
            }

            selectedWordEl = null;
            selectedDefEl = null;
            matchingCorrect = matched.length;
            updateScore();
        }
    }

    function resetMatching() {
        if (!vocabItems.length) return;
        buildMatchingSection();
        matchingCorrect = 0;
        updateScore();
    }

    /************* PRONUNCIATION LOGIC *************/
    function recordPronunciation(index) {
        if (!vocabItems.length) {
            alert("Generate an exercise first.");
            return;
        }
        if (!recognition) {
            alert("Speech recognition is not supported in this browser.");
            return;
        }

        const fb = document.getElementById(`pron-feed-${index}`);
        fb.textContent = "Listening...";
        fb.className = "feedback";
        fb.style.display = "block";

        recognition.start();

        recognition.onresult = function(e) {
            const spoken = (e.results[0][0].transcript || "").toLowerCase().trim();
            const expected = activePronSentences[index].pronAnswer.toLowerCase().trim();
            const spokenNorm = normalizeAnswer(spoken);
            const expectedNorm = normalizeAnswer(expected);
            const blank = document.getElementById(`pron-blank-${index}`);

            if (spokenNorm === expectedNorm && expectedNorm !== "") {
                fb.textContent = `✔️ Correct! You said "${spoken}".`;
                fb.className = "feedback correct";
                blank.textContent = activePronSentences[index].pronAnswer;
                blank.style.color = "green";

                if (!pronFlags[index]) {
                    pronFlags[index] = true;
                    pronCorrect = pronFlags.filter(Boolean).length;
                    updateScore();
                }
            } else {
                fb.textContent = `❌ You said: "${spoken}". Try again.`;
                fb.className = "feedback incorrect";
            }
        };

        recognition.onerror = function() {
            fb.textContent = "❌ Error during recognition. Please try again.";
            fb.className = "feedback incorrect";
        };
    }

    /************* NAVIGATION *************/
    function showSection(btn, id) {
        document.querySelectorAll(".game-section").forEach(s => s.classList.remove("active"));
        document.getElementById(id).classList.add("active");

        document.querySelectorAll(".nav-btn").forEach(b => b.classList.remove("active"));
        if (btn) btn.classList.add("active");
    }

    /************* INIT *************/
    // Nothing to do on load; user must click "Generate Exercise"
    updateScore();
</script>

</body>
</html>
