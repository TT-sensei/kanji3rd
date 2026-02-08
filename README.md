<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>3ねんせい かんじマスター</title>
    
    <script src="https://cdn.jsdelivr.net/npm/hanzi-writer@3.5/dist/hanzi-writer.min.js"></script>
    
    <link href="https://fonts.googleapis.com/css2?family=Mochiy+Pop+One&display=swap" rel="stylesheet">

    <style>
        /* --- 基本設定 --- */
        body {
            font-family: 'Mochiy Pop One', sans-serif;
            background-color: #E0F7FA; /* 背景を薄い水色に変更 */
            margin: 0;
            padding: 0;
            min-height: 100vh;
            color: #006064; /* 文字色を濃い青緑に変更 */
            overflow-x: hidden;
            touch-action: manipulation;
            user-select: none;
        }

        /* 画面切り替え */
        .screen {
            display: none;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            width: 100%;
            min-height: 100vh;
            padding: 20px;
            box-sizing: border-box;
            opacity: 0;
            transition: opacity 0.3s;
        }

        .screen.active {
            display: flex;
            opacity: 1;
        }

        /* --- 1. タイトル画面 --- */
        #title-screen {
            /* グラデーションを青系に変更 */
            background: linear-gradient(135deg, #4DD0E1 0%, #0097A7 100%);
        }

        .game-title {
            font-size: 3rem;
            color: #FFF;
            text-shadow: 3px 3px 0px #006064;
            text-align: center;
            margin-bottom: 40px;
            line-height: 1.2;
        }

        .mode-btn {
            width: 280px;
            font-size: 1.5rem;
            padding: 15px 0;
            border-radius: 50px;
            border: none;
            cursor: pointer;
            transition: transform 0.1s;
            margin: 10px 0;
            color: white;
            font-family: inherit;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            box-shadow: 0 6px 0 rgba(0,0,0,0.2);
        }

        .mode-btn:active { transform: scale(0.95); box-shadow: 0 3px 0 rgba(0,0,0,0.2); }
        .btn-practice { background-color: #66BB6A; } /* 緑に変更 */
        .btn-test { background-color: #FF7043; } /* オレンジに変更 */

        /* --- 2. 一覧画面 --- */
        #list-screen {
            justify-content: flex-start;
            padding-top: 20px;
        }

        .header-info {
            background: white;
            padding: 10px 20px;
            border-radius: 20px;
            box-shadow: 0 4px 10px rgba(0,0,0,0.1);
            margin-bottom: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
            width: 90%;
            max-width: 600px;
        }
        
        .mode-badge {
            font-size: 1rem;
            padding: 5px 15px;
            border-radius: 20px;
            color: white;
            margin-bottom: 5px;
            font-weight: bold;
        }

        .kanji-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(60px, 1fr)); /* 漢字が多いので少し小さく */
            gap: 10px;
            width: 100%;
            max-width: 800px;
            padding-bottom: 50px;
        }

        .kanji-card {
            background: white;
            aspect-ratio: 1 / 1;
            border-radius: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.8rem;
            cursor: pointer;
            box-shadow: 0 4px 0px #B0BEC5;
            position: relative;
            transition: transform 0.1s;
            border: 2px solid transparent;
        }

        .kanji-card:active { transform: translateY(2px); box-shadow: 0 2px 0px #B0BEC5; }

        .mark {
            position: absolute;
            top: -8px; right: -8px;
            font-size: 1.2rem;
            text-shadow: 1px 1px 0 #FFF;
            display: none;
            z-index: 2;
        }

        /* クリア時のスタイル */
        .kanji-card.cleared-practice { background-color: #E8F5E9; border-color: #66BB6A; }
        .kanji-card.cleared-practice .star-mark { display: block; }
        .kanji-card.cleared-test { background-color: #FCE4EC; border-color: #EC407A; }
        .kanji-card.cleared-test .crown-mark { display: block; }

        /* --- 3. 練習/テスト画面 --- */
        #practice-screen {
            background-color: #E1F5FE;
            position: relative;
        }
        #practice-screen.test-bg { background-color: #FFF3E0; }

        .practice-header {
            width: 100%;
            max-width: 500px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }

        .nav-btn {
            background: #546E7A;
            color: white;
            border: none;
            padding: 8px 16px;
            border-radius: 20px;
            cursor: pointer;
            font-family: inherit;
            box-shadow: 0 3px 0 #37474F;
        }
        .nav-btn:active { transform: translateY(2px); box-shadow: 0 1px 0 #37474F; }

        .target-char-display {
            font-size: 2rem;
            color: #006064;
            font-weight: bold;
        }

        .canvas-container {
            background: white;
            padding: 15px;
            border-radius: 20px;
            box-shadow: 0 8px 0px #90A4AE;
            margin-bottom: 20px;
            position: relative;
        }

        #character-target-div {
            width: 300px;
            height: 300px;
            touch-action: none;
        }

        #message-area {
            height: 40px;
            font-size: 1.2rem;
            color: #333;
            text-align: center;
            font-weight: bold;
        }

        /* アニメーション */
        @keyframes pop {
            0% { transform: scale(0.5); opacity: 0; }
            50% { transform: scale(1.2); }
            100% { transform: scale(1); opacity: 1; }
        }
        .reward-animation {
            position: absolute;
            top: 50%; left: 50%;
            transform: translate(-50%, -50%);
            font-size: 10rem;
            animation: pop 0.5s ease-out;
            pointer-events: none;
            display: none;
            z-index: 100;
            text-shadow: 0 0 20px rgba(255, 255, 255, 0.8);
        }

        /* --- 結果メニュー --- */
        #result-overlay {
            position: absolute;
            top: 0; left: 0;
            width: 100%; height: 100%;
            background: rgba(255, 255, 255, 0.95);
            display: none;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            z-index: 200;
        }
        
        #result-overlay.active { display: flex; }

        .result-title {
            font-size: 2.5rem;
            margin-bottom: 30px;
            text-shadow: 2px 2px 0 #FFF;
        }

        .result-btn {
            width: 240px;
            padding: 15px;
            margin: 10px 0;
            border: none;
            border-radius: 50px;
            font-size: 1.3rem;
            font-family: inherit;
            color: white;
            cursor: pointer;
            box-shadow: 0 5px 0 rgba(0,0,0,0.2);
            transition: transform 0.1s;
            font-weight: bold;
        }
        .result-btn:active { transform: scale(0.95); box-shadow: 0 2px 0 rgba(0,0,0,0.2); }

        .btn-next { background: #26A69A; }
        .btn-retry { background: #FFA726; }
        .btn-home { background: #78909C; }

    </style>
</head>
<body>

    <div id="title-screen" class="screen active">
        <div class="game-title">3ねんせい<br>かんじ<br>マスター</div>
        
        <button class="mode-btn btn-practice" onclick="selectMode('practice')">
            <span>⭐</span> れんしゅう
        </button>
        <button class="mode-btn btn-test" onclick="selectMode('test')">
            <span>👑</span> テスト
        </button>
    </div>

    <div id="list-screen" class="screen">
        <div class="header-info">
            <div id="mode-display" class="mode-badge"></div>
            <div style="font-size:1.2rem;">
                あつめた数： <span id="star-counter" style="font-size:1.5rem; font-weight:bold;">0</span> / 200
            </div>
            <button class="nav-btn" onclick="showScreen('title-screen')" style="margin-top:10px; font-size:0.8rem;">タイトルへ</button>
        </div>
        
        <div class="kanji-grid" id="kanji-grid">
            </div>
        
        <button class="nav-btn" onclick="resetData()" style="margin-top:30px; background:#B0BEC5; font-size:0.7rem;">データを消す</button>
    </div>

    <div id="practice-screen" class="screen">
        <div class="practice-header">
            <button class="nav-btn" onclick="showScreen('list-screen')">もどる</button>
            <div id="current-reading" class="target-char-display">よみ</div>
            <div style="width: 60px;"></div>
        </div>

        <div class="canvas-container">
            <div id="character-target-div"></div>
            <div id="anim-star" class="reward-animation">⭐</div>
            <div id="anim-crown" class="reward-animation">👑</div>
        </div>

        <div id="message-area">なぞってみよう！</div>

        <div id="result-overlay">
            <div class="result-title" id="result-title-text">できた！</div>
            <button class="result-btn btn-next" onclick="goNext()">つぎの かんじ ▶</button>
            <button class="result-btn btn-retry" onclick="retry()">もういちど ↺</button>
            <button class="result-btn btn-home" onclick="showScreen('list-screen')">ホームにもどる 🏠</button>
        </div>

        <button class="nav-btn" onclick="retry()" style="background:#FF7043; margin-top:10px; z-index:10;">書き直す</button>
    </div>

    <script>
        // --- データ設定（小学3年生200字） ---
        const kanjiData = [
            {char:'悪',reading:'わる(い)'},{char:'安',reading:'やす(い)'},{char:'暗',reading:'くら(い)'},{char:'医',reading:'い'},{char:'委',reading:'い'},
            {char:'意',reading:'い'},{char:'育',reading:'そだ(つ)'},{char:'員',reading:'いん'},{char:'院',reading:'いん'},{char:'飲',reading:'の(む)'},
            {char:'運',reading:'はこ(ぶ)'},{char:'泳',reading:'およ(ぐ)'},{char:'駅',reading:'えき'},{char:'央',reading:'おう'},{char:'横',reading:'よこ'},
            {char:'屋',reading:'や'},{char:'温',reading:'あたた(かい)'},{char:'化',reading:'ば(ける)'},{char:'荷',reading:'に'},{char:'界',reading:'かい'},
            {char:'開',reading:'あ(ける)'},{char:'階',reading:'かい'},{char:'寒',reading:'さむ(い)'},{char:'感',reading:'かん'},{char:'漢',reading:'かん'},
            {char:'館',reading:'かん'},{char:'岸',reading:'きし'},{char:'起',reading:'お(きる)'},{char:'期',reading:'き'},{char:'客',reading:'きゃく'},
            {char:'究',reading:'きゅう'},{char:'急',reading:'いそ(ぐ)'},{char:'級',reading:'きゅう'},{char:'宮',reading:'みや'},{char:'球',reading:'たま'},
            {char:'去',reading:'さ(る)'},{char:'橋',reading:'はし'},{char:'業',reading:'ぎょう'},{char:'曲',reading:'ま(がる)'},{char:'局',reading:'きょく'},
            {char:'銀',reading:'ぎん'},{char:'区',reading:'く'},{char:'苦',reading:'くる(しい)'},{char:'具',reading:'ぐ'},{char:'君',reading:'きみ'},
            {char:'係',reading:'かかり'},{char:'軽',reading:'かる(い)'},{char:'血',reading:'ち'},{char:'決',reading:'き(める)'},{char:'研',reading:'けん'},
            {char:'県',reading:'けん'},{char:'庫',reading:'こ'},{char:'湖',reading:'みずうみ'},{char:'向',reading:'む(かう)'},{char:'幸',reading:'しあわ(せ)'},
            {char:'港',reading:'みなと'},{char:'号',reading:'ごう'},{char:'根',reading:'ね'},{char:'祭',reading:'まつ(り)'},{char:'皿',reading:'さら'},
            {char:'仕',reading:'し'},{char:'死',reading:'し'},{char:'使',reading:'つか(う)'},{char:'始',reading:'はじ(める)'},{char:'指',reading:'ゆび'},
            {char:'歯',reading:'は'},{char:'詩',reading:'し'},{char:'次',reading:'つぎ'},{char:'事',reading:'こと'},{char:'持',reading:'も(つ)'},
            {char:'式',reading:'しき'},{char:'実',reading:'み'},{char:'写',reading:'うつ(す)'},{char:'者',reading:'もの'},{char:'主',reading:'ぬし'},
            {char:'守',reading:'まも(る)'},{char:'取',reading:'と(る)'},{char:'酒',reading:'さけ'},{char:'受',reading:'う(ける)'},{char:'州',reading:'しゅう'},
            {char:'拾',reading:'ひろ(う)'},{char:'終',reading:'お(わる)'},{char:'習',reading:'なら(う)'},{char:'集',reading:'あつ(める)'},{char:'住',reading:'す(む)'},
            {char:'重',reading:'おも(い)'},{char:'宿',reading:'やど'},{char:'所',reading:'ところ'},{char:'暑',reading:'あつ(い)'},{char:'助',reading:'たす(ける)'},
            {char:'昭',reading:'しょう'},{char:'消',reading:'き(える)'},{char:'商',reading:'しょう'},{char:'章',reading:'しょう'},{char:'勝',reading:'か(つ)'},
            {char:'乗',reading:'の(る)'},{char:'植',reading:'う(える)'},{char:'申',reading:'もう(す)'},{char:'身',reading:'み'},{char:'神',reading:'かみ'},
            {char:'真',reading:'ま'},{char:'深',reading:'ふか(い)'},{char:'進',reading:'すす(む)'},{char:'世',reading:'よ'},{char:'整',reading:'ととの(う)'},
            {char:'昔',reading:'むかし'},{char:'全',reading:'ぜん'},{char:'相',reading:'あい'},{char:'送',reading:'おく(る)'},{char:'想',reading:'そう'},
            {char:'息',reading:'いき'},{char:'速',reading:'はや(い)'},{char:'族',reading:'ぞく'},{char:'他',reading:'ほか'},{char:'打',reading:'う(つ)'},
            {char:'対',reading:'たい'},{char:'待',reading:'ま(つ)'},{char:'代',reading:'か(わる)'},{char:'第',reading:'だい'},{char:'題',reading:'だい'},
            {char:'炭',reading:'すみ'},{char:'短',reading:'みじか(い)'},{char:'談',reading:'だん'},{char:'着',reading:'き(る)'},{char:'注',reading:'そそ(ぐ)'},
            {char:'柱',reading:'はしら'},{char:'丁',reading:'ちょう'},{char:'帳',reading:'ちょう'},{char:'調',reading:'しら(べる)'},{char:'追',reading:'お(う)'},
            {char:'定',reading:'てい'},{char:'庭',reading:'にわ'},{char:'笛',reading:'ふえ'},{char:'鉄',reading:'てつ'},{char:'転',reading:'ころ(ぶ)'},
            {char:'都',reading:'みやこ'},{char:'度',reading:'ど'},{char:'投',reading:'な(げる)'},{char:'豆',reading:'まめ'},{char:'島',reading:'しま'},
            {char:'湯',reading:'ゆ'},{char:'登',reading:'のぼ(る)'},{char:'等',reading:'ひと(しい)'},{char:'動',reading:'うご(く)'},{char:'童',reading:'どう'},
            {char:'農',reading:'のう'},{char:'波',reading:'なみ'},{char:'配',reading:'くば(る)'},{char:'倍',reading:'ばい'},{char:'箱',reading:'はこ'},
            {char:'畑',reading:'はたけ'},{char:'発',reading:'はつ'},{char:'反',reading:'はん'},{char:'坂',reading:'さか'},{char:'板',reading:'いた'},
            {char:'皮',reading:'かわ'},{char:'悲',reading:'かな(しい)'},{char:'美',reading:'うつく(しい)'},{char:'鼻',reading:'はな'},{char:'筆',reading:'ふで'},
            {char:'氷',reading:'こおり'},{char:'表',reading:'おもて'},{char:'秒',reading:'びょう'},{char:'病',reading:'やまい'},{char:'品',reading:'しな'},
            {char:'負',reading:'ま(ける)'},{char:'部',reading:'ぶ'},{char:'服',reading:'ふく'},{char:'福',reading:'ふく'},{char:'物',reading:'もの'},
            {char:'平',reading:'たい(ら)'},{char:'返',reading:'かえ(す)'},{char:'勉',reading:'べん'},{char:'放',reading:'はな(す)'},{char:'味',reading:'あじ'},
            {char:'命',reading:'いのち'},{char:'面',reading:'めん'},{char:'問',reading:'と(い)'},{char:'役',reading:'やく'},{char:'薬',reading:'くすり'},
            {char:'由',reading:'ゆ'},{char:'油',reading:'あぶら'},{char:'有',reading:'あ(る)'},{char:'遊',reading:'あそ(ぶ)'},{char:'予',reading:'よ'},
            {char:'羊',reading:'ひつじ'},{char:'洋',reading:'よう'},{char:'葉',reading:'は'},{char:'陽',reading:'ひ'},{char:'様',reading:'さま'},
            {char:'落',reading:'お(ちる)'},{char:'流',reading:'なが(れる)'},{char:'旅',reading:'たび'},{char:'両',reading:'りょう'},{char:'緑',reading:'みどり'},
            {char:'礼',reading:'れい'},{char:'列',reading:'れつ'},{char:'練',reading:'ね(る)'},{char:'路',reading:'みち'},{char:'和',reading:'わ'}
        ];

        // 状態管理（3年生用にキーを変更して、1年生のデータと混ざらないようにしています）
        let progressPractice = JSON.parse(localStorage.getItem('kanjiMasterPractice3')) || {};
        let progressTest = JSON.parse(localStorage.getItem('kanjiMasterTest3')) || {};
        
        let writer;
        let currentChar = null;
        let currentMode = 'practice'; // 'practice' or 'test'

        // --- 画面遷移 ---
        function showScreen(screenId) {
            document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
            document.getElementById(screenId).classList.add('active');
            
            if (screenId === 'list-screen') {
                renderList();
            }
        }

        // --- モード選択 ---
        function selectMode(mode) {
            currentMode = mode;
            showScreen('list-screen');
        }

        // --- 一覧画面の描画 ---
        function renderList() {
            const grid = document.getElementById('kanji-grid');
            grid.innerHTML = '';
            
            const badge = document.getElementById('mode-display');
            const counter = document.getElementById('star-counter');
            let count = 0;
            const targetProgress = (currentMode === 'practice') ? progressPractice : progressTest;

            if (currentMode === 'practice') {
                badge.textContent = "⭐ れんしゅうモード";
                badge.style.backgroundColor = "#66BB6A";
            } else {
                badge.textContent = "👑 テストモード";
                badge.style.backgroundColor = "#FF7043";
            }

            kanjiData.forEach(item => {
                const isCleared = targetProgress[item.char];
                if (isCleared) count++;

                const card = document.createElement('div');
                card.className = `kanji-card`;
                if (isCleared) {
                    card.classList.add(currentMode === 'practice' ? 'cleared-practice' : 'cleared-test');
                }
                
                card.innerHTML = `
                    ${item.char}
                    <div class="mark star-mark">⭐</div>
                    <div class="mark crown-mark">👑</div>
                `;
                
                card.onclick = () => startApp(item);
                grid.appendChild(card);
            });

            counter.innerText = count;
        }

        // --- アプリ開始（練習orテスト） ---
        function startApp(item) {
            currentChar = item;
            showScreen('practice-screen');

            // メニューを隠す
            document.getElementById('result-overlay').classList.remove('active');

            // 背景色とメッセージの切り替え
            const screen = document.getElementById('practice-screen');
            const msgArea = document.getElementById('message-area');
            const readingDisplay = document.getElementById('current-reading');
            
            if (currentMode === 'test') {
                screen.classList.add('test-bg');
                msgArea.innerText = "この かんじ を かこう！";
                msgArea.style.color = "#BF360C";
                
                readingDisplay.innerText = item.reading;
                readingDisplay.style.fontSize = "3rem";
            } else {
                screen.classList.remove('test-bg');
                msgArea.innerText = "うすいせんを なぞろう！";
                msgArea.style.color = "#006064";
                
                readingDisplay.innerText = `${item.char} (${item.reading})`;
                readingDisplay.style.fontSize = "2rem";
            }

            // UIリセット
            document.getElementById('character-target-div').innerHTML = '';
            document.getElementById('anim-star').style.display = 'none';
            document.getElementById('anim-crown').style.display = 'none';

            const isTest = (currentMode === 'test');

            // --- HanziWriter 設定 ---
            writer = HanziWriter.create('character-target-div', item.char, {
                width: 300,
                height: 300,
                padding: 10,
                showOutline: !isTest, 
                strokeAnimationSpeed: 1,
                delayBetweenStrokes: 200,
                radicalColor: '#00796B', // 部首の色も少し大人っぽく
                drawingWidth: 25,
                outlineColor: '#DDD',
                strokeColor: '#333',
                highlightColor: '#FF7043',
                showHintAfterMisses: 1,
                
                charDataLoader: (char, onComplete) => {
                    fetch(`https://cdn.jsdelivr.net/gh/chanind/hanzi-writer-data-jp@master/data/${char}.json`)
                        .then(res => {
                            if (!res.ok) throw new Error('JP Data not found');
                            return res.json();
                        })
                        .then(data => onComplete(data))
                        .catch(err => {
                            console.log("JP Data not found, fallback to default.");
                            fetch(`https://cdn.jsdelivr.net/npm/hanzi-writer-data@2.0/${char}.json`)
                                .then(res => res.json())
                                .then(data => onComplete(data));
                        });
                }
            });

            startQuiz();
        }

        // --- クイズロジック ---
        function startQuiz() {
            writer.quiz({
                onMistake: function(strokeData) {
                    const msg = document.getElementById('message-area');
                    if (currentMode === 'test') {
                        msg.innerText = "おしい！ ヒントをみてね";
                    } else {
                        msg.innerText = "ここだよ！";
                    }
                    msg.style.color = "#D32F2F";
                },
                onCorrectStroke: function(strokeData) {
                    const msg = document.getElementById('message-area');
                    msg.innerText = "いいね！";
                    msg.style.color = "#388E3C";
                },
                onComplete: function(summaryData) {
                    handleComplete();
                }
            });
        }

        // --- クリア処理 ---
        function handleComplete() {
            const msg = document.getElementById('message-area');
            const resultTitle = document.getElementById('result-title-text');
            
            if (currentMode === 'practice') {
                msg.innerText = "できたー！ ⭐ゲット！";
                resultTitle.innerText = "できたー！⭐";
                resultTitle.style.color = "#66BB6A";
                document.getElementById('anim-star').style.display = 'block';
                
                progressPractice[currentChar.char] = true;
                localStorage.setItem('kanjiMasterPractice3', JSON.stringify(progressPractice));
            } else {
                msg.innerText = "すごい！！ 👑ゲット！";
                resultTitle.innerText = "すごい！👑";
                resultTitle.style.color = "#FF7043";
                document.getElementById('anim-crown').style.display = 'block';
                
                progressTest[currentChar.char] = true;
                localStorage.setItem('kanjiMasterTest3', JSON.stringify(progressTest));
            }

            // 少し待ってからメニューを表示
            setTimeout(() => {
                document.getElementById('result-overlay').classList.add('active');
            }, 1000);
        }

        // --- 次の漢字へ進む機能 ---
        function goNext() {
            const currentIndex = kanjiData.findIndex(k => k.char === currentChar.char);
            
            if (currentIndex >= 0 && currentIndex < kanjiData.length - 1) {
                startApp(kanjiData[currentIndex + 1]);
            } else {
                alert("これでおしまい！ すごい！");
                showScreen('list-screen');
            }
        }

        function retry() {
            startApp(currentChar);
        }

        function resetData() {
            if (confirm("すべてのデータを けしますか？")) {
                localStorage.removeItem('kanjiMasterPractice3');
                localStorage.removeItem('kanjiMasterTest3');
                progressPractice = {};
                progressTest = {};
                renderList();
            }
        }

    </script>
</body>
</html>
