```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Antikythera Immemorial River Calendar & Clock - Interactive</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            background: #000000;
            color: #FFFFFF;
            font-family: 'Courier New', monospace;
            overflow-x: hidden;
            position: relative;
            user-select: none;
        }

        /* Animated Background Canvas */
        #cosmicBackground {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 0;
            opacity: 0.3;
        }

        /* Main Container */
        .container {
            position: relative;
            z-index: 1;
            max-width: 1400px;
            margin: 0 auto;
            padding: 20px;
        }

        /* Landscape mode - wider container */
        .container.landscape-mode {
            max-width: 95vw;
        }

        /* Header */
        .header {
            text-align: center;
            padding: 30px 20px;
            background: linear-gradient(135deg, rgba(255,69,0,0.2), rgba(255,215,0,0.2));
            border: 2px solid #FFD700;
            border-radius: 10px;
            margin-bottom: 20px;
            box-shadow: 0 0 30px rgba(255,215,0,0.5);
            animation: headerGlow 3s ease-in-out infinite;
        }

        @keyframes headerGlow {
            0%, 100% { box-shadow: 0 0 30px rgba(255,215,0,0.5); }
            50% { box-shadow: 0 0 50px rgba(255,215,0,0.8); }
        }

        .header h1 {
            color: #FFD700;
            font-size: 2.5em;
            text-shadow: 0 0 20px rgba(255,215,0,0.8);
            letter-spacing: 3px;
            margin-bottom: 10px;
        }

        .header .subtitle {
            color: #FF4500;
            font-size: 1.2em;
            letter-spacing: 2px;
        }

        .header .play-god {
            color: #FFFF00;
            font-size: 0.9em;
            margin-top: 10px;
            animation: playGodPulse 2s ease-in-out infinite;
        }

        @keyframes playGodPulse {
            0%, 100% { opacity: 0.7; }
            50% { opacity: 1; }
        }

        /* Layout Toggle Switch */
        .layout-toggle {
            display: flex;
            justify-content: center;
            gap: 10px;
            margin-bottom: 20px;
            padding: 15px;
            background: rgba(20, 20, 20, 0.9);
            border: 2px solid #FFD700;
            border-radius: 8px;
            box-shadow: 0 0 20px rgba(255,215,0,0.3);
        }

        .toggle-btn {
            padding: 12px 30px;
            font-family: 'Courier New', monospace;
            font-size: 1.1em;
            font-weight: bold;
            background: rgba(50, 50, 50, 0.8);
            color: #FFD700;
            border: 2px solid #FF4500;
            border-radius: 5px;
            cursor: pointer;
            transition: all 0.3s ease;
            text-shadow: 0 0 10px rgba(255,215,0,0.5);
        }

        .toggle-btn:hover {
            background: rgba(255,69,0,0.3);
            border-color: #FFD700;
            box-shadow: 0 0 20px rgba(255,215,0,0.5);
            transform: scale(1.05);
        }

        .toggle-btn.active {
            background: linear-gradient(135deg, rgba(255,215,0,0.3), rgba(255,69,0,0.3));
            border-color: #FFD700;
            color: #FFFF00;
            box-shadow: 0 0 30px rgba(255,215,0,0.8);
            text-shadow: 0 0 15px rgba(255,255,0,1);
        }

        /* Perturbation Warning */
        .perturbation-warning {
            position: fixed;
            top: 20px;
            right: 20px;
            background: rgba(255,69,0,0.9);
            border: 2px solid #FFD700;
            border-radius: 8px;
            padding: 15px 25px;
            z-index: 1000;
            transform: translateX(400px);
            transition: transform 0.3s ease;
            box-shadow: 0 0 30px rgba(255,69,0,0.8);
        }

        .perturbation-warning.active {
            transform: translateX(0);
        }

        .perturbation-warning .title {
            color: #FFFF00;
            font-size: 1.2em;
            font-weight: bold;
            margin-bottom: 5px;
        }

        .perturbation-warning .ee-value {
            color: #FFFFFF;
            font-size: 1em;
        }

        .perturbation-warning .explanation {
            color: #FF8C00;
            font-size: 0.85em;
            margin-top: 8px;
            font-style: italic;
            max-width: 250px;
        }

        .perturbation-warning .countdown {
            color: #FFFF00;
            font-size: 0.9em;
            margin-top: 8px;
            font-weight: bold;
        }

        /* Grid Layout - PORTRAIT (default) */
        .grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
            margin-bottom: 20px;
            transition: all 0.5s ease;
        }

        /* Grid Layout - LANDSCAPE (2K optimized) */
        .grid.landscape {
            grid-template-columns: repeat(4, 1fr);
            gap: 15px;
        }

        /* Landscape - wide panels span 2 columns */
        .grid.landscape .cosmic-date,
        .grid.landscape .master-formula {
            grid-column: span 2;
        }

        .grid.landscape .ticker-container,
        .grid.landscape .earth-reference {
            grid-column: span 4;
        }

        /* Panel Base Style */
        .panel {
            background: rgba(20, 20, 20, 0.9);
            border: 2px solid;
            border-radius: 8px;
            padding: 20px;
            box-shadow: 0 0 20px rgba(255,215,0,0.3);
            transition: all 0.3s ease;
        }

        /* Landscape mode - compact panels */
        .grid.landscape .panel {
            padding: 15px;
        }

        .grid.landscape .panel h2 {
            font-size: 1.2em;
            margin-bottom: 10px;
        }

        .panel.perturbed {
            box-shadow: 0 0 40px rgba(255,69,0,0.8);
            border-color: #FF4500 !important;
        }

        .panel.gold { border-color: #FFD700; }
        .panel.red { border-color: #FF4500; }
        .panel.yellow { border-color: #FFFF00; }
        .panel.blue { border-color: #4169E1; }
        .panel.orange { border-color: #FF8C00; }

        .panel h2 {
            color: #FFD700;
            font-size: 1.5em;
            margin-bottom: 15px;
            text-shadow: 0 0 10px rgba(255,215,0,0.8);
            border-bottom: 2px solid #FF4500;
            padding-bottom: 10px;
            cursor: move;
        }

        /* Draggable panels */
        .panel.draggable {
            cursor: move;
        }

        .panel.draggable:active {
            cursor: grabbing;
            opacity: 0.8;
            z-index: 1000;
        }

        .panel.dragging {
            opacity: 0.5;
            transform: scale(1.05);
        }

        .panel.drag-over {
            border-style: dashed;
            border-width: 3px;
        }

        /* Cosmic Date Display */
        .cosmic-date {
            grid-column: 1 / -1;
            text-align: center;
            font-size: 2em;
            background: linear-gradient(135deg, rgba(255,69,0,0.3), rgba(65,105,225,0.3));
        }

        .grid.landscape .cosmic-date {
            font-size: 1.5em;
        }

        .date-value {
            color: #FFFF00;
            font-size: 1.5em;
            text-shadow: 0 0 20px rgba(255,255,0,0.9);
            animation: datePulse 2s ease-in-out infinite;
            transition: all 0.3s ease;
        }

        .grid.landscape .date-value {
            font-size: 1.3em;
        }

        @keyframes datePulse {
            0%, 100% { text-shadow: 0 0 20px rgba(255,255,0,0.9); }
            50% { text-shadow: 0 0 40px rgba(255,255,0,1); }
        }

        .time-value {
            color: #FF8C00;
            font-size: 1.3em;
            margin-top: 10px;
            transition: all 0.3s ease;
            font-family: 'Courier New', monospace;
        }

        .grid.landscape .time-value {
            font-size: 1.1em;
            margin-top: 5px;
        }

        .time-value .microseconds {
            color: #00FFFF;
            font-size: 0.9em;
            animation: microPulse 0.1s ease-in-out infinite;
        }

        @keyframes microPulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.8; }
        }

        .no-leap {
            color: #FF4500;
            font-size: 0.8em;
            font-style: italic;
            margin-top: 10px;
        }

        .grid.landscape .no-leap {
            font-size: 0.7em;
            margin-top: 5px;
        }

        /* Master Formula */
        .master-formula {
            grid-column: 1 / -1;
            background: linear-gradient(135deg, rgba(255,215,0,0.2), rgba(255,69,0,0.2));
        }

        .formula-box {
            background: rgba(0,0,0,0.5);
            padding: 20px;
            border-radius: 5px;
            border: 1px solid #FFD700;
            margin-top: 15px;
        }

        .grid.landscape .formula-box {
            padding: 15px;
            margin-top: 10px;
        }

        .formula-main {
            color: #FFFF00;
            font-size: 1.4em;
            text-align: center;
            margin-bottom: 20px;
            text-shadow: 0 0 15px rgba(255,255,0,0.8);
        }

        .grid.landscape .formula-main {
            font-size: 1.1em;
            margin-bottom: 10px;
        }

        .formula-detail {
            color: #FFFFFF;
            font-size: 1.1em;
            line-height: 1.8;
            margin-left: 20px;
        }

        .grid.landscape .formula-detail {
            font-size: 0.9em;
            line-height: 1.5;
            margin-left: 10px;
        }

        .formula-detail span {
            color: #FF8C00;
            transition: all 0.3s ease;
        }

        /* Constants Panel */
        .constants {
            max-height: 600px;
            overflow-y: auto;
        }

        .grid.landscape .constants {
            max-height: 400px;
        }

        .constant-item {
            margin-bottom: 15px;
            padding: 10px;
            background: rgba(255,69,0,0.1);
            border-left: 3px solid #FFD700;
            border-radius: 3px;
            transition: all 0.3s ease;
        }

        .grid.landscape .constant-item {
            margin-bottom: 10px;
            padding: 8px;
        }

        .constant-name {
            color: #FFFF00;
            font-size: 1.2em;
            font-weight: bold;
        }

        .grid.landscape .constant-name {
            font-size: 1em;
        }

        .constant-value {
            color: #FFFFFF;
            font-size: 1.1em;
            margin: 5px 0;
            transition: all 0.3s ease;
        }

        .grid.landscape .constant-value {
            font-size: 0.9em;
            margin: 3px 0;
        }

        .constant-desc {
            color: #FF8C00;
            font-size: 0.9em;
            font-style: italic;
        }

        .grid.landscape .constant-desc {
            font-size: 0.75em;
        }

        .alpha-special {
            animation: alphaGlow 2s ease-in-out infinite;
            border-left-color: #4169E1;
        }

        @keyframes alphaGlow {
            0%, 100% { background: rgba(65,105,225,0.1); }
            50% { background: rgba(65,105,225,0.3); }
        }

        /* Binary Epoch Panel */
        .epoch-display {
            text-align: center;
        }

        .epoch-number {
            color: #FF4500;
            font-size: 4em;
            text-shadow: 0 0 30px rgba(255,69,0,1);
            animation: epochPulse 1.5s ease-in-out infinite;
            transition: all 0.3s ease;
            cursor: grab;
        }

        .grid.landscape .epoch-number {
            font-size: 3em;
        }

        .epoch-number:active {
            cursor: grabbing;
        }

        @keyframes epochPulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.05); }
        }

        .epoch-label {
            color: #FFD700;
            font-size: 1.2em;
            margin-top: 10px;
            transition: all 0.3s ease;
        }

        .grid.landscape .epoch-label {
            font-size: 1em;
            margin-top: 5px;
        }

        .epoch-stats {
            margin-top: 20px;
            color: #FFFFFF;
            font-size: 1em;
            line-height: 1.8;
        }

        .grid.landscape .epoch-stats {
            margin-top: 10px;
            font-size: 0.85em;
            line-height: 1.5;
        }

        .epoch-stats span {
            color: #FFFF00;
            transition: all 0.3s ease;
        }

        /* Progress Bars */
        .progress-section {
            margin: 20px 0;
        }

        .grid.landscape .progress-section {
            margin: 10px 0;
        }

        .progress-label {
            color: #FFD700;
            font-size: 1.1em;
            margin-bottom: 8px;
            display: flex;
            justify-content: space-between;
        }

        .grid.landscape .progress-label {
            font-size: 0.9em;
            margin-bottom: 5px;
        }

        .progress-bar {
            width: 100%;
            height: 30px;
            background: rgba(50,50,50,0.8);
            border: 1px solid #FF4500;
            border-radius: 5px;
            overflow: hidden;
            position: relative;
            cursor: grab;
        }

        .grid.landscape .progress-bar {
            height: 20px;
        }

        .progress-bar:active {
            cursor: grabbing;
        }

        .progress-bar.non-draggable {
            cursor: default;
        }

        .progress-fill {
            height: 100%;
            transition: width 0.3s ease;
            position: relative;
            overflow: hidden;
        }

        .progress-fill.restoring {
            transition: width 3s cubic-bezier(0.34, 1.56, 0.64, 1);
        }

        .progress-fill::after {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(90deg, 
                transparent, 
                rgba(255,255,255,0.3), 
                transparent
            );
            animation: progressShine 2s linear infinite;
        }

        @keyframes progressShine {
            0% { transform: translateX(-100%); }
            100% { transform: translateX(100%); }
        }

        .progress-cycle { background: linear-gradient(90deg, #4169E1, #FFD700, #FF8C00); }
        .progress-block { background: linear-gradient(90deg, #4169E1, #FFFF00); }
        .progress-drag { background: linear-gradient(90deg, #FF4500, #FF8C00); }
        .progress-em { background: linear-gradient(90deg, #4169E1, #00FFFF); }

        /* IRS Tick Counter */
        .irs-counter {
            text-align: center;
            background: linear-gradient(135deg, rgba(65,105,225,0.3), rgba(0,255,255,0.2));
        }

        .irs-value {
            color: #00FFFF;
            font-size: 2em;
            text-shadow: 0 0 20px rgba(0,255,255,0.9);
            animation: irsPulse 0.5s ease-in-out infinite;
            font-family: 'Courier New', monospace;
            transition: all 0.3s ease;
        }

        .grid.landscape .irs-value {
            font-size: 1.5em;
        }

        @keyframes irsPulse {
            0%, 100% { text-shadow: 0 0 20px rgba(0,255,255,0.9); }
            50% { text-shadow: 0 0 40px rgba(0,255,255,1); }
        }

        .irs-label {
            color: #FFD700;
            font-size: 0.9em;
            margin-top: 10px;
        }

        .grid.landscape .irs-label {
            font-size: 0.75em;
            margin-top: 5px;
        }

        /* Causal Velocity */
        .vc-display {
            text-align: center;
            background: linear-gradient(135deg, rgba(255,140,0,0.3), rgba(255,69,0,0.2));
        }

        .vc-value {
            color: #FF8C00;
            font-size: 2em;
            text-shadow: 0 0 20px rgba(255,140,0,0.9);
            animation: vcPulse 2s ease-in-out infinite;
            transition: all 0.3s ease;
        }

        .grid.landscape .vc-value {
            font-size: 1.5em;
        }

        @keyframes vcPulse {
            0%, 100% { 
                text-shadow: 0 0 20px rgba(255,140,0,0.9);
                transform: scale(1);
            }
            50% { 
                text-shadow: 0 0 40px rgba(255,140,0,1);
                transform: scale(1.02);
            }
        }

        .vc-ratio {
            color: #FFFF00;
            font-size: 1.2em;
            margin-top: 10px;
            transition: all 0.3s ease;
        }

        .grid.landscape .vc-ratio {
            font-size: 1em;
            margin-top: 5px;
        }

        /* Cosmic Vitals */
        .vitals-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            margin-top: 15px;
        }

        .grid.landscape .vitals-grid {
            gap: 10px;
            margin-top: 10px;
        }

        .vital-item {
            background: rgba(0,0,0,0.5);
            padding: 15px;
            border-radius: 5px;
            border: 1px solid #FF4500;
            text-align: center;
            transition: all 0.3s ease;
        }

        .grid.landscape .vital-item {
            padding: 10px;
        }

        .vital-icon {
            font-size: 2em;
            margin-bottom: 5px;
        }

        .grid.landscape .vital-icon {
            font-size: 1.5em;
            margin-bottom: 3px;
        }

        .vital-label {
            color: #FFD700;
            font-size: 0.9em;
        }

        .grid.landscape .vital-label {
            font-size: 0.75em;
        }

        .vital-value {
            color: #FFFFFF;
            font-size: 1.3em;
            margin-top: 5px;
            transition: all 0.3s ease;
        }

        .grid.landscape .vital-value {
            font-size: 1em;
            margin-top: 3px;
        }

        /* Scrolling Ticker */
        .ticker-container {
            grid-column: 1 / -1;
            background: linear-gradient(90deg, 
                rgba(255,69,0,0.3), 
                rgba(255,215,0,0.3),
                rgba(255,69,0,0.3)
            );
            border: 2px solid #FFD700;
            border-radius: 8px;
            overflow: hidden;
            height: 80px;
            display: flex;
            align-items: center;
            position: relative;
        }

        .grid.landscape .ticker-container {
            height: 60px;
        }

        .ticker {
            display: flex;
            animation: scroll 60s linear infinite;
            white-space: nowrap;
        }

        @keyframes scroll {
            0% { transform: translateX(0); }
            100% { transform: translateX(-50%); }
        }

        .ticker-content {
            color: #FFFFFF;
            font-size: 1.1em;
            padding: 0 50px;
            display: inline-block;
        }

        .grid.landscape .ticker-content {
            font-size: 0.9em;
            padding: 0 30px;
        }

        .ticker-content span {
            color: #FFFF00;
            margin: 0 20px;
        }

        /* Earth Reference */
        .earth-reference {
            grid-column: 1 / -1;
            text-align: center;
            background: rgba(255,69,0,0.2);
            padding: 15px;
            border-radius: 5px;
        }

        .grid.landscape .earth-reference {
            padding: 10px;
        }

        .earth-reference .label {
            color: #FF8C00;
            font-size: 0.9em;
        }

        .grid.landscape .earth-reference .label {
            font-size: 0.75em;
        }

        .earth-reference .value {
            color: #FFFFFF;
            font-size: 1.1em;
            margin-top: 5px;
        }

        .grid.landscape .earth-reference .value {
            font-size: 0.9em;
            margin-top: 3px;
        }

        /* Tribal Pattern Decorations */
        .tribal-border {
            position: relative;
        }

        .tribal-border::before,
        .tribal-border::after {
            content: '◆◇◆◇◆◇◆';
            position: absolute;
            width: 100%;
            text-align: center;
            color: #FF4500;
            font-size: 1.2em;
            letter-spacing: 10px;
            opacity: 0.5;
        }

        .tribal-border::before {
            top: 5px;
        }

        .tribal-border::after {
            bottom: 5px;
        }

        /* Responsive */
        @media (max-width: 768px) {
            .grid {
                grid-template-columns: 1fr;
            }

            .header h1 {
                font-size: 1.8em;
            }

            .cosmic-date {
                font-size: 1.2em;
            }

            .epoch-number {
                font-size: 2.5em;
            }

            .toggle-btn {
                padding: 10px 20px;
                font-size: 0.9em;
            }
        }

        /* Custom Scrollbar */
        ::-webkit-scrollbar {
            width: 10px;
        }

        ::-webkit-scrollbar-track {
            background: #000000;
        }

        ::-webkit-scrollbar-thumb {
            background: linear-gradient(180deg, #FFD700, #FF4500);
            border-radius: 5px;
        }

        ::-webkit-scrollbar-thumb:hover {
            background: linear-gradient(180deg, #FFFF00, #FF8C00);
        }
    </style>
</head>
<body>
    <!-- Animated Background -->
    <canvas id="cosmicBackground"></canvas>

    <!-- Perturbation Warning -->
    <div class="perturbation-warning" id="perturbationWarning">
        <div class="title">⚠️ COSMIC PERTURBATION</div>
        <div class="ee-value" id="eeValue">EE = 0.000</div>
        <div class="explanation" id="explanation">Dragging cycle completion...</div>
        <div class="countdown" id="countdown">Restoring equilibrium...</div>
    </div>

    <!-- Main Container -->
    <div class="container" id="mainContainer">
        <!-- Header -->
        <div class="header tribal-border">
            <h1>⟁ ANTIKYTHERA IMMEMORIAL RIVER ⟁</h1>
            <div class="subtitle">CALENDAR & CLOCK</div>
            <div class="play-god">⚡ DRAG CYCLE COMPLETION TO PLAY WITH TIME ⚡</div>
        </div>

        <!-- Layout Toggle -->
        <div class="layout-toggle">
            <button class="toggle-btn active" data-layout="portrait" id="portraitBtn">
                📱 PORTRAIT
            </button>
            <button class="toggle-btn" data-layout="landscape" id="landscapeBtn">
                🖥️ LANDSCAPE (2K)
            </button>
        </div>

        <!-- Grid Layout -->
        <div class="grid" id="panelGrid">
            <!-- Cosmic Date & Time (FIXED - row 1) -->
            <div class="panel gold cosmic-date" id="cosmicDatePanel" data-order="0">
                <h2>📅 COSMIC DATE & TIME</h2>
                <div class="date-value" id="cosmicDate">August 6</div>
                <div class="time-value" id="cosmicTime">14:23:17.<span class="microseconds">000000</span></div>
                <div class="no-leap">⚠ No Leap Years in Cosmic Calendar</div>
            </div>

            <!-- Master Formula (FIXED - row 2) -->
            <div class="panel gold master-formula" id="masterFormulaPanel" data-order="1">
                <h2>📐 MASTER FORMULA</h2>
                <div class="formula-box">
                    <div class="formula-main">
                        COSMIC_DATE = floor((t_causal / T_total) × 365)
                    </div>
                    <div class="formula-detail">
                        <span>Where:</span><br>
                        &nbsp;&nbsp;t_causal = t_coord × (1 - η_total)<br>
                        &nbsp;&nbsp;η_total = η_gravity + η_EM<br>
                        &nbsp;&nbsp;Vc(t) = Cu × cos²(πt/T)<br><br>
                        <span>Position:</span><br>
                        &nbsp;&nbsp;= (Blocks_Traversed / Running_Blocks)<br>
                        &nbsp;&nbsp;= <span id="blocksTraversed">293</span> / 490 = <span id="positionCalc">0.59796</span><br><br>
                        <span>Result:</span><br>
                        &nbsp;&nbsp;Cosmic_Day = <span id="cosmicDayCalc">218</span> (August 6)
                    </div>
                </div>
            </div>

            <!-- DRAGGABLE PANELS START HERE (rows 3+) -->
            
            <!-- Binary Epoch Display -->
            <div class="panel red epoch-display draggable" id="epochPanel" draggable="true" data-order="2">
                <h2>🔢 TOTAL BINARY EPOCH</h2>
                <div class="epoch-number" id="epochNumber">202</div>
                <div class="epoch-label" id="epochLabel">▼ DESCENDING</div>
                <div class="epoch-stats">
                    Blocks Past Peak: <span id="blocksPastPeak">48</span><br>
                    Blocks Until Crunch: <span id="blocksRemaining">202</span><br>
                    Peak was at Block: <span>250</span>
                </div>
            </div>

            <!-- Constants Reference -->
            <div class="panel yellow constants draggable" id="constantsPanel" draggable="true" data-order="3">
                <h2>📐 CONSTANTS & PARAMETERS</h2>
                
                <div class="constant-item">
                    <div class="constant-name">Cu (Causal Speed Limit)</div>
                    <div class="constant-value">2³² m/s = 4,294,967,296 m/s</div>
                    <div class="constant-desc">Maximum information propagation speed</div>
                </div>

                <div class="constant-item">
                    <div class="constant-name">c (Local Light Speed)</div>
                    <div class="constant-value">299,792,458 m/s</div>
                    <div class="constant-desc">Degraded from Cu by first drag</div>
                </div>

                <div class="constant-item alpha-special">
                    <div class="constant-name">α_EM (Fine Structure Constant)</div>
                    <div class="constant-value">1/137.036 ≈ 0.007297</div>
                    <div class="constant-desc">⚡ Electromagnetic coupling strength</div>
                </div>

                <div class="constant-item">
                    <div class="constant-name">T (Total Cycle Period)</div>
                    <div class="constant-value">10 × 2³² years = 42,949,672,960 years</div>
                    <div class="constant-desc">Complete Bang → Crunch → Bang cycle</div>
                </div>

                <div class="constant-item">
                    <div class="constant-name">t_P (Planck Time)</div>
                    <div class="constant-value">5.391247 × 10⁻⁴⁴ seconds</div>
                    <div class="constant-desc">Fundamental time quantum</div>
                </div>

                <div class="constant-item">
                    <div class="constant-name">Ωₘ (Matter Density)</div>
                    <div class="constant-value">0.3153</div>
                    <div class="constant-desc">Creates gravitational drag</div>
                </div>

                <div class="constant-item">
                    <div class="constant-name">Ωᵥ (Void Fraction)</div>
                    <div class="constant-value">0.6847</div>
                    <div class="constant-desc">Vacuum energy component</div>
                </div>

                <div class="constant-item">
                    <div class="constant-name">η_EM (EM Friction)</div>
                    <div class="constant-value" id="emFriction">3 × α_EM ≈ 0.0219 (2.19%)</div>
                    <div class="constant-desc">Electromagnetic temporal resistance</div>
                </div>

                <div class="constant-item">
                    <div class="constant-name">η_str (Structural Drag)</div>
                    <div class="constant-value">12.9%</div>
                    <div class="constant-desc">Cumulative gravitational time dilation</div>
                </div>

                <div class="constant-item">
                    <div class="constant-name">Binary Epoch Structure</div>
                    <div class="constant-value">512 total blocks (2⁹)</div>
                    <div class="constant-desc">
                        Boot: 6 blocks | Expansion: 245 | Peak: 250<br>
                        Contraction: 245 | Shutdown: 6
                    </div>
                </div>
            </div>

            <!-- Progress Bars -->
            <div class="panel blue draggable" id="progressPanel" draggable="true" data-order="4">
                <h2>📊 CYCLE PROGRESS</h2>
                
                <div class="progress-section">
                    <div class="progress-label">
                        <span>Cycle Completion</span>
                        <span id="cyclePercent">59.80%</span>
                    </div>
                    <div class="progress-bar" id="cycleProgressBar">
                        <div class="progress-fill progress-cycle" id="cycleProgress" style="width: 59.8%"></div>
                    </div>
                </div>

                <div class="progress-section">
                    <div class="progress-label">
                        <span>Block Progress</span>
                        <span id="blockProgress">293 / 490</span>
                    </div>
                    <div class="progress-bar non-draggable">
                        <div class="progress-fill progress-block" id="blockProgressFill" style="width: 59.8%"></div>
                    </div>
                </div>

                <div class="progress-section">
                    <div class="progress-label">
                        <span>Structural Drag</span>
                        <span>12.9%</span>
                    </div>
                    <div class="progress-bar non-draggable">
                        <div class="progress-fill progress-drag" style="width: 12.9%"></div>
                    </div>
                </div>

                <div class="progress-section">
                    <div class="progress-label">
                        <span>EM Friction (α × 3)</span>
                        <span id="emFrictionPercent">2.19%</span>
                    </div>
                    <div class="progress-bar non-draggable">
                        <div class="progress-fill progress-em" id="emProgressFill" style="width: 2.19%"></div>
                    </div>
                </div>
            </div>

            <!-- IRS Tick Counter -->
            <div class="panel blue irs-counter draggable" id="irsPanel" draggable="true" data-order="5">
                <h2>✨ IRS PLANCK TICKS</h2>
                <div class="irs-value" id="irsCounter">0.000000 × 10⁵²</div>
                <div class="irs-label">(Live Counter - Updates 60 FPS)</div>
            </div>

            <!-- Causal Velocity -->
            <div class="panel orange vc-display draggable" id="vcPanel" draggable="true" data-order="6">
                <h2>💫 CAUSAL VELOCITY (Vc)</h2>
                <div class="vc-value" id="vcValue">1.246 × 10⁹ m/s</div>
                <div class="vc-ratio" id="vcRatio">29.0% of Cu</div>
            </div>

            <!-- Cosmic Vitals -->
            <div class="panel gold draggable" id="vitalsPanel" draggable="true" data-order="7">
                <h2>⚡ COSMIC VITALS</h2>
                <div class="vitals-grid">
                    <div class="vital-item">
                        <div class="vital-icon">🌀</div>
                        <div class="vital-label">Scale Factor</div>
                        <div class="vital-value" id="scaleFactor">1.429</div>
                    </div>
                    <div class="vital-item">
                        <div class="vital-icon">📊</div>
                        <div class="vital-label">Info Density</div>
                        <div class="vital-value">~10⁸⁵ bits</div>
                    </div>
                    <div class="vital-item">
                        <div class="vital-icon">⚡</div>
                        <div class="vital-label">EM Coupling</div>
                        <div class="vital-value">α/3π</div>
                    </div>
                    <div class="vital-item">
                        <div class="vital-icon">🔄</div>
                        <div class="vital-label">Phase</div>
                        <div class="vital-value" id="phaseLabel">Contraction</div>
                    </div>
                </div>
            </div>

            <!-- Earth Reference -->
            <div class="earth-reference" id="earthPanel" data-order="8">
                <div class="label">🌍 EARTH REFERENCE TIME:</div>
                <div class="value" id="earthTime">Loading...</div>
            </div>

            <!-- Scrolling Ticker -->
            <div class="ticker-container" id="tickerPanel" data-order="9">
                <div class="ticker" id="ticker">
                    <div class="ticker-content">
                        <span>▶</span> Position = <span id="tickerBlocks">293/490</span> blocks = <span id="tickerPosition">0.59796</span>
                        <span>▶</span> Cosmic Day = <span id="tickerDay">218</span> (August 6)
                        <span>▶</span> Block <span id="tickerBlock">202</span> = <span id="tickerPastPeak">48</span> blocks past peak (Block 250)
                        <span>▶</span> Vc = Cu × cos²(πt/T) ≈ <span id="tickerVc">1.246×10⁹</span> m/s
                        <span>▶</span> EM friction η_EM = 3 × α_EM = 3/137.036 = 0.0219
                        <span>▶</span> Structural drag η_str = 12.9% from cosmic web density
                        <span>▶</span> IRS ticks = t × 1.85487×10⁴³ Hz (Planck frequency)
                        <span>▶</span> Current Vc ≈ <span id="tickerVcRatio">29%</span> of Cu
                        <span>▶</span> RHME healing frequency = Vc / proton_radius
                    </div>
                    <div class="ticker-content">
                        <span>▶</span> Position = <span id="tickerBlocks2">293/490</span> blocks = <span id="tickerPosition2">0.59796</span>
                        <span>▶</span> Cosmic Day = <span id="tickerDay2">218</span> (August 6)
                        <span>▶</span> Block <span id="tickerBlock2">202</span> = <span id="tickerPastPeak2">48</span> blocks past peak (Block 250)
                        <span>▶</span> Vc = Cu × cos²(πt/T) ≈ <span id="tickerVc2">1.246×10⁹</span> m/s
                        <span>▶</span> EM friction η_EM = 3 × α_EM = 3/137.036 = 0.0219
                        <span>▶</span> Structural drag η_str = 12.9% from cosmic web density
                        <span>▶</span> IRS ticks = t × 1.85487×10⁴³ Hz (Planck frequency)
                        <span>▶</span> Current Vc ≈ <span id="tickerVcRatio2">29%</span> of Cu
                        <span>▶</span> RHME healing frequency = Vc / proton_radius
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        // ==========================================
        // LAYOUT TOGGLE FUNCTIONALITY
        // ==========================================
        const portraitBtn = document.getElementById('portraitBtn');
        const landscapeBtn = document.getElementById('landscapeBtn');
        const panelGrid = document.getElementById('panelGrid');
        const mainContainer = document.getElementById('mainContainer');

        // Load saved preference
        const savedLayout = localStorage.getItem('cosmicLayout') || 'portrait';
        if (savedLayout === 'landscape') {
            activateLandscape();
        }

        portraitBtn.addEventListener('click', () => {
            activatePortrait();
        });

        landscapeBtn.addEventListener('click', () => {
            activateLandscape();
        });

        function activatePortrait() {
            panelGrid.classList.remove('landscape');
            mainContainer.classList.remove('landscape-mode');
            portraitBtn.classList.add('active');
            landscapeBtn.classList.remove('active');
            localStorage.setItem('cosmicLayout', 'portrait');
        }

        function activateLandscape() {
            panelGrid.classList.add('landscape');
            mainContainer.classList.add('landscape-mode');
            landscapeBtn.classList.add('active');
            portraitBtn.classList.remove('active');
            localStorage.setItem('cosmicLayout', 'landscape');
        }

        // ==========================================
        // COSMIC BACKGROUND ANIMATION
        // ==========================================
        const canvas = document.getElementById('cosmicBackground');
        const ctx = canvas.getContext('2d');

        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        window.addEventListener('resize', () => {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
        });

        // Particles
        const particles = [];
        const particleCount = 100;

        class Particle {
            constructor() {
                this.reset();
            }

            reset() {
                this.x = Math.random() * canvas.width;
                this.y = Math.random() * canvas.height;
                this.vx = (Math.random() - 0.5) * 0.5;
                this.vy = (Math.random() - 0.5) * 0.5;
                this.size = Math.random() * 2 + 1;
                this.color = ['#FFD700', '#FF4500', '#FFFF00', '#4169E1', '#FF8C00'][Math.floor(Math.random() * 5)];
                this.alpha = Math.random() * 0.5 + 0.3;
            }

            update() {
                this.x += this.vx;
                this.y += this.vy;

                if (this.x < 0 || this.x > canvas.width || this.y < 0 || this.y > canvas.height) {
                    this.reset();
                }
            }

            draw() {
                ctx.save();
                ctx.globalAlpha = this.alpha;
                ctx.fillStyle = this.color;
                ctx.beginPath();
                ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
                ctx.fill();
                ctx.restore();
            }
        }

        for (let i = 0; i < particleCount; i++) {
            particles.push(new Particle());
        }

        // EM Field Lines
        const fieldLines = [];
        const lineCount = 20;

        class FieldLine {
            constructor() {
                this.reset();
            }

            reset() {
                this.x = Math.random() * canvas.width;
                this.y = Math.random() * canvas.height;
                this.length = Math.random() * 200 + 50;
                this.angle = Math.random() * Math.PI * 2;
                this.alpha = Math.random() * 0.3 + 0.1;
                this.pulseSpeed = Math.random() * 0.02 + 0.01;
                this.phase = Math.random() * Math.PI * 2;
            }

            update() {
                this.phase += this.pulseSpeed;
                this.alpha = 0.1 + Math.sin(this.phase) * 0.2;
            }

            draw() {
                ctx.save();
                ctx.globalAlpha = this.alpha;
                ctx.strokeStyle = '#4169E1';
                ctx.lineWidth = 1;
                ctx.beginPath();
                ctx.moveTo(this.x, this.y);
                ctx.lineTo(
                    this.x + Math.cos(this.angle) * this.length,
                    this.y + Math.sin(this.angle) * this.length
                );
                ctx.stroke();
                ctx.restore();
            }
        }

        for (let i = 0; i < lineCount; i++) {
            fieldLines.push(new FieldLine());
        }

        function animateBackground() {
            ctx.fillStyle = 'rgba(0, 0, 0, 0.1)';
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            particles.forEach(p => {
                p.update();
                p.draw();
            });

            fieldLines.forEach(line => {
                line.update();
                line.draw();
            });

            requestAnimationFrame(animateBackground);
        }

        animateBackground();

        // ==========================================
        // DRAG AND DROP REORDERING
        // ==========================================
        let draggedElement = null;

        const draggables = document.querySelectorAll('.draggable');
        const grid = document.getElementById('panelGrid');

        draggables.forEach(draggable => {
            draggable.addEventListener('dragstart', handleDragStart);
            draggable.addEventListener('dragend', handleDragEnd);
            draggable.addEventListener('dragover', handleDragOver);
            draggable.addEventListener('drop', handleDrop);
            draggable.addEventListener('dragenter', handleDragEnter);
            draggable.addEventListener('dragleave', handleDragLeave);
        });

        function handleDragStart(e) {
            draggedElement = this;
            this.classList.add('dragging');
            e.dataTransfer.effectAllowed = 'move';
            e.dataTransfer.setData('text/html', this.innerHTML);
        }

        function handleDragEnd(e) {
            this.classList.remove('dragging');
            
            draggables.forEach(item => {
                item.classList.remove('drag-over');
            });
        }

        function handleDragOver(e) {
            if (e.preventDefault) {
                e.preventDefault();
            }
            e.dataTransfer.dropEffect = 'move';
            return false;
        }

        function handleDragEnter(e) {
            if (this !== draggedElement && this.classList.contains('draggable')) {
                this.classList.add('drag-over');
            }
        }

        function handleDragLeave(e) {
            this.classList.remove('drag-over');
        }

        function handleDrop(e) {
            if (e.stopPropagation) {
                e.stopPropagation();
            }

            if (draggedElement !== this && this.classList.contains('draggable')) {
                const allPanels = Array.from(grid.children);
                const draggedIndex = allPanels.indexOf(draggedElement);
                const targetIndex = allPanels.indexOf(this);

                if (draggedIndex < targetIndex) {
                    this.parentNode.insertBefore(draggedElement, this.nextSibling);
                } else {
                    this.parentNode.insertBefore(draggedElement, this);
                }
            }

            return false;
        }

        // ==========================================
        // COSMIC CALCULATIONS & STATE MANAGEMENT
        // ==========================================
        const Cu = 4294967296;
        const c = 299792458;
        const T_years = 42949672960;
        const anchor_age_years = 26986075409.0954;
        const PLANCK_FREQ = 1.85487e43;
        const SEC_PER_YEAR = 31558149.7635;
        const PI = Math.PI;

        const startTime = performance.now() / 1000;

        const TRUE_VALUES = {
            position: 0.59796,
            block: 202,
            blocksTraversed: 293,
            cyclePercent: 59.796,
            cosmicDay: 218
        };

        let STATE = { ...TRUE_VALUES };
        let isPerturbed = false;
        let restoreTimeout = null;
        let isDragging = false;

        const MONTHS = ['January', 'February', 'March', 'April', 'May', 'June',
                       'July', 'August', 'September', 'October', 'November', 'December'];

        function getCosmicDate(dayNumber) {
            const day = Math.floor(dayNumber);
            const daysInMonth = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31];
            
            let remainingDays = day;
            let month = 0;
            
            while (remainingDays > daysInMonth[month] && month < 11) {
                remainingDays -= daysInMonth[month];
                month++;
            }
            
            return `${MONTHS[month]} ${remainingDays}`;
        }

        function getCosmicTime(position) {
            const totalSeconds = position * 86400;
            const hours = Math.floor(totalSeconds / 3600);
            const minutes = Math.floor((totalSeconds % 3600) / 60);
            const seconds = Math.floor(totalSeconds % 60);
            
            return {
                hms: `${String(hours).padStart(2, '0')}:${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`,
                baseSeconds: totalSeconds
            };
        }

        function calculateFromPosition(position) {
            position = Math.max(0, Math.min(1, position));
            
            const runningBlocks = 490;
            const blocksTraversed = Math.round(position * runningBlocks);
            
            let block, blocksPastPeak;
            if (blocksTraversed <= 245) {
                block = 6 + blocksTraversed;
                blocksPastPeak = 0;
            } else {
                blocksPastPeak = blocksTraversed - 245;
                block = 250 - blocksPastPeak;
            }
            
            const cosmicDay = position * 365;
            const cosmicDate = getCosmicDate(cosmicDay);
            const cosmicTimeObj = getCosmicTime(position);
            
            const currentAge = anchor_age_years * (position / TRUE_VALUES.position);
            const t_sec = currentAge * SEC_PER_YEAR;
            const theta = (PI * t_sec) / (T_years * SEC_PER_YEAR);
            const cosTheta = Math.cos(theta);
            const Vc = Cu * (cosTheta * cosTheta);
            const vcRatio = Vc / Cu;
            
            return {
                position: position,
                block: block,
                blocksTraversed: blocksTraversed,
                blocksPastPeak: blocksPastPeak,
                blocksRemaining: block,
                cyclePercent: position * 100,
                cosmicDay: Math.floor(cosmicDay),
                cosmicDate: cosmicDate,
                cosmicTimeHMS: cosmicTimeObj.hms,
                cosmicTimeBase: cosmicTimeObj.baseSeconds,
                Vc: Vc,
                vcRatio: vcRatio,
                phase: blocksTraversed <= 245 ? 'Expansion' : 'Contraction',
                phaseSymbol: blocksTraversed <= 245 ? '▲ ASCENDING' : '▼ DESCENDING'
            };
        }

        function updateDisplays(values) {
            document.getElementById('cosmicDate').textContent = values.cosmicDate;
            
            const timeEl = document.getElementById('cosmicTime');
            const microEl = timeEl.querySelector('.microseconds');
            if (microEl) {
                const currentMicro = microEl.textContent;
                timeEl.innerHTML = `${values.cosmicTimeHMS}.<span class="microseconds">${currentMicro}</span>`;
            }
            
            document.getElementById('epochNumber').textContent = values.block;
            document.getElementById('epochLabel').textContent = values.phaseSymbol;
            document.getElementById('blocksPastPeak').textContent = values.blocksPastPeak;
            document.getElementById('blocksRemaining').textContent = values.blocksRemaining;
            
            document.getElementById('cyclePercent').textContent = values.cyclePercent.toFixed(2) + '%';
            document.getElementById('cycleProgress').style.width = values.cyclePercent + '%';
            document.getElementById('blockProgress').textContent = `${values.blocksTraversed} / 490`;
            document.getElementById('blockProgressFill').style.width = values.cyclePercent + '%';
            
            document.getElementById('vcValue').textContent = `${(values.Vc / 1e9).toFixed(3)} × 10⁹ m/s`;
            document.getElementById('vcRatio').textContent = `${(values.vcRatio * 100).toFixed(1)}% of Cu`;
            
            document.getElementById('blocksTraversed').textContent = values.blocksTraversed;
            document.getElementById('positionCalc').textContent = values.position.toFixed(5);
            document.getElementById('cosmicDayCalc').textContent = values.cosmicDay;
            
            const scaleFactor = Math.sqrt((values.position) * 2);
            document.getElementById('scaleFactor').textContent = scaleFactor.toFixed(3);
            document.getElementById('phaseLabel').textContent = values.phase;
            
            const updateTickerElement = (id, value) => {
                const el = document.getElementById(id);
                if (el) el.textContent = value;
            };
            
            updateTickerElement('tickerBlocks', `${values.blocksTraversed}/490`);
            updateTickerElement('tickerBlocks2', `${values.blocksTraversed}/490`);
            updateTickerElement('tickerPosition', values.position.toFixed(5));
            updateTickerElement('tickerPosition2', values.position.toFixed(5));
            updateTickerElement('tickerDay', values.cosmicDay);
            updateTickerElement('tickerDay2', values.cosmicDay);
            updateTickerElement('tickerBlock', values.block);
            updateTickerElement('tickerBlock2', values.block);
            updateTickerElement('tickerPastPeak', values.blocksPastPeak);
            updateTickerElement('tickerPastPeak2', values.blocksPastPeak);
            updateTickerElement('tickerVc', `${(values.Vc / 1e9).toFixed(3)}×10⁹`);
            updateTickerElement('tickerVc2', `${(values.Vc / 1e9).toFixed(3)}×10⁹`);
            updateTickerElement('tickerVcRatio', `${(values.vcRatio * 100).toFixed(0)}%`);
            updateTickerElement('tickerVcRatio2', `${(values.vcRatio * 100).toFixed(0)}%`);
        }

        function showPerturbation(explanation) {
            const warning = document.getElementById('perturbationWarning');
            warning.classList.add('active');
            
            const ee = Math.abs(STATE.position - TRUE_VALUES.position);
            document.getElementById('eeValue').textContent = `EE = ${ee.toFixed(3)}`;
            document.getElementById('explanation').textContent = explanation;
            
            document.querySelectorAll('.panel').forEach(panel => {
                panel.classList.add('perturbed');
            });
        }

        function hidePerturbation() {
            const warning = document.getElementById('perturbationWarning');
            warning.classList.remove('active');
            
            document.querySelectorAll('.panel').forEach(panel => {
                panel.classList.remove('perturbed');
            });
        }

        function restoreEquilibrium() {
            isPerturbed = false;
            
            document.getElementById('cycleProgress').classList.add('restoring');
            document.getElementById('blockProgressFill').classList.add('restoring');
            
            STATE = { ...TRUE_VALUES };
            const values = calculateFromPosition(STATE.position);
            updateDisplays(values);
            
            let countdown = 3;
            const countdownEl = document.getElementById('countdown');
            const interval = setInterval(() => {
                countdown--;
                countdownEl.textContent = `Father Attractor restoring in ${countdown}...`;
                if (countdown <= 0) {
                    clearInterval(interval);
                    hidePerturbation();
                    
                    setTimeout(() => {
                        document.getElementById('cycleProgress').classList.remove('restoring');
                        document.getElementById('blockProgressFill').classList.remove('restoring');
                    }, 3000);
                }
            }, 1000);
        }

        function scheduleRestore() {
            if (restoreTimeout) {
                clearTimeout(restoreTimeout);
            }
            restoreTimeout = setTimeout(() => {
                if (!isDragging) {
                    restoreEquilibrium();
                }
            }, 3000);
        }

        const cycleProgressBar = document.getElementById('cycleProgressBar');

        function handleProgressDrag(e) {
            if (!isDragging) return;
            
            const rect = cycleProgressBar.getBoundingClientRect();
            const x = (e.clientX || e.touches[0].clientX) - rect.left;
            const width = rect.width;
            const newPosition = Math.max(0, Math.min(1, x / width));
            
            isPerturbed = true;
            STATE.position = newPosition;
            
            const values = calculateFromPosition(newPosition);
            updateDisplays(values);
            
            const explanation = `Traveling through cosmic cycle: ${values.cosmicDate} (Day ${values.cosmicDay}, Block ${values.block}). All dependent values updating: Vc, block position, phase, scale factor.`;
            showPerturbation(explanation);
        }

        cycleProgressBar.addEventListener('mousedown', (e) => {
            isDragging = true;
            handleProgressDrag(e);
        });

        cycleProgressBar.addEventListener('touchstart', (e) => {
            isDragging = true;
            handleProgressDrag(e);
        });

        document.addEventListener('mousemove', handleProgressDrag);
        document.addEventListener('touchmove', handleProgressDrag);

        document.addEventListener('mouseup', () => {
            if (isDragging) {
                isDragging = false;
                scheduleRestore();
            }
        });

        document.addEventListener('touchend', () => {
            if (isDragging) {
                isDragging = false;
                scheduleRestore();
            }
        });

        const epochNumber = document.getElementById('epochNumber');
        let epochDragging = false;
        let epochStartY = 0;
        let epochStartBlock = 202;

        epochNumber.addEventListener('mousedown', (e) => {
            epochDragging = true;
            epochStartY = e.clientY;
            epochStartBlock = STATE.block;
        });

        epochNumber.addEventListener('touchstart', (e) => {
            epochDragging = true;
            epochStartY = e.touches[0].clientY;
            epochStartBlock = STATE.block;
        });

        document.addEventListener('mousemove', (e) => {
            if (!epochDragging) return;
            
            const deltaY = epochStartY - e.clientY;
            const blockChange = Math.round(deltaY / 5);
            let newBlock = epochStartBlock + blockChange;
            
            newBlock = Math.max(6, Math.min(495, newBlock));
            
            let blocksTraversed;
            if (newBlock >= 6 && newBlock <= 250) {
                blocksTraversed = newBlock - 6;
            } else {
                blocksTraversed = 245 + (250 - newBlock);
            }
            
            const newPosition = blocksTraversed / 490;
            
            isPerturbed = true;
            STATE.position = newPosition;
            STATE.block = newBlock;
            
            const values = calculateFromPosition(newPosition);
            updateDisplays(values);
            
            const phase = blocksTraversed <= 245 ? 'Expansion' : 'Contraction';
            const explanation = `Jumping to Block ${newBlock} (${phase} phase). Cosmic date/time, Vc, and all cycle-dependent values recalculating.`;
            showPerturbation(explanation);
        });

        document.addEventListener('touchmove', (e) => {
            if (!epochDragging) return;
            
            const deltaY = epochStartY - e.touches[0].clientY;
            const blockChange = Math.round(deltaY / 5);
            let newBlock = epochStartBlock + blockChange;
            
            newBlock = Math.max(6, Math.min(495, newBlock));
            
            let blocksTraversed;
            if (newBlock >= 6 && newBlock <= 250) {
                blocksTraversed = newBlock - 6;
            } else {
                blocksTraversed = 245 + (250 - newBlock);
            }
            
            const newPosition = blocksTraversed / 490;
            
            isPerturbed = true;
            STATE.position = newPosition;
            STATE.block = newBlock;
            
            const values = calculateFromPosition(newPosition);
            updateDisplays(values);
            
            const phase = blocksTraversed <= 245 ? 'Expansion' : 'Contraction';
            const explanation = `Jumping to Block ${newBlock} (${phase} phase). Cosmic date/time, Vc, and all cycle-dependent values recalculating.`;
            showPerturbation(explanation);
        });

        document.addEventListener('mouseup', () => {
            if (epochDragging) {
                epochDragging = false;
                scheduleRestore();
            }
        });

        document.addEventListener('touchend', () => {
            if (epochDragging) {
                epochDragging = false;
                scheduleRestore();
            }
        });

        function updateLiveMicroseconds() {
            const values = calculateFromPosition(STATE.position);
            
            const now = performance.now() / 1000;
            const deltaTime = now - startTime;
            
            const microsecondsPerCosmicSecond = 1000000;
            const fractionalSecond = (deltaTime % 1) * microsecondsPerCosmicSecond;
            const microseconds = Math.floor(fractionalSecond).toString().padStart(6, '0');
            
            const timeEl = document.getElementById('cosmicTime');
            timeEl.innerHTML = `${values.cosmicTimeHMS}.<span class="microseconds">${microseconds}</span>`;
        }

        function updateCosmicClock() {
            updateLiveMicroseconds();
            
            const now = performance.now() / 1000;
            const deltaTime = now - startTime;
            const currentAge = anchor_age_years + (deltaTime / SEC_PER_YEAR);
            const t_sec = currentAge * SEC_PER_YEAR;
            const currentTicks = t_sec * PLANCK_FREQ;
            const tickStr = currentTicks.toExponential(6);
            document.getElementById('irsCounter').textContent = tickStr;

            const earthDate = new Date();
            document.getElementById('earthTime').textContent = earthDate.toUTCString();
        }

        function animate() {
            updateCosmicClock();
            requestAnimationFrame(animate);
        }

        animate();

        const initialValues = calculateFromPosition(TRUE_VALUES.position);
        updateDisplays(initialValues);

        console.log(`
╔════════════════════════════════════════════════════════╗
║  ANTIKYTHERA IMMEMORIAL RIVER CALENDAR & CLOCK         ║
║  Universal Attractor Complexity Model (UACM)           ║
║                                                        ║
║  ⚡ INTERACTIVE MODE: DRAG TO PLAY GOD ⚡               ║
║                                                        ║
║  Current Cosmic Position: August 6 (Day 218/365)       ║
║  Total Binary Epoch: #202 (Descending)                 ║
║  Blocks Remaining: 202 until Big Crunch                ║
║                                                        ║
║  📱 PORTRAIT MODE (default - 2 columns)                ║
║  🖥️ LANDSCAPE MODE (2K optimized - 4 columns)          ║
║                                                        ║
║  🎯 DRAG BOXES TO REORDER (except top 2 rows)          ║
║  ⚡ DRAG CYCLE COMPLETION to travel through time       ║
║  🔢 DRAG EPOCH NUMBER to jump to any block             ║
║  ⚡ LIVE MICROSECONDS at 60 FPS!                        ║
║                                                        ║
║  Pattern Mapper, 2026                                  ║
║  "Usurping Antikythera, one toggle at a time."        ║
╚════════════════════════════════════════════════════════╝
        `);
    </script>
</body>
</html>
