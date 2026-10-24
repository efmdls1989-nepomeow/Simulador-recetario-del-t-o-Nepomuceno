<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simulador Terapéutico del Tío Nepomuceno - v12</title>
    <!-- jsPDF para exportar reportes clínicos -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #12181b;
            color: #dcdde1;
            margin: 0;
            padding: 20px;
        }
        .container {
            max-width: 850px;
            margin: 0 auto;
            background: #1e272c;
            padding: 30px;
            border-radius: 12px;
            box-shadow: 0 8px 24px rgba(0,0,0,0.6);
            border: 1px solid #2f3640;
        }
        .top-bar {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
            flex-wrap: wrap;
            gap: 10px;
        }
        .lang-box {
            background: #2f3640;
            border: 1px solid #00d2d3;
            color: #f1f2f6;
            padding: 8px 12px;
            border-radius: 6px;
            font-size: 0.9em;
            cursor: pointer;
        }
        /* ESTILOS DEL GATITO ANIMADO */
        .cat-avatar-container {
            display: flex;
            justify-content: center;
            align-items: center;
            margin-bottom: 15px;
        }
        .animated-cat {
            width: 90px;
            height: 90px;
            animation: floatCat 3s ease-in-out infinite;
        }
        @keyframes floatCat {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-6px); }
        }
        .cat-tail {
            animation: tailWag 1.5s ease-in-out infinite alternate;
            transform-origin: 55px 70px;
        }
        @keyframes tailWag {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(18deg); }
        }
        .cat-blink {
            animation: blinkEyes 4s infinite;
            transform-origin: center;
        }
        @keyframes blinkEyes {
            0%, 96%, 100% { transform: scaleY(1); }
            98% { transform: scaleY(0.1); }
        }
        .cat-heart {
            animation: pulseHeart 1.2s infinite;
            transform-origin: center;
        }
        @keyframes pulseHeart {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.15); }
        }

        h1 { color: #00d2d3; text-align: center; margin-bottom: 5px; font-size: 1.8em; }
        .subtitle { text-align: center; color: #718093; font-size: 0.95em; margin-bottom: 20px; }
        .disclaimer {
            background: #2f3542;
            border-left: 5px solid #ffa502;
            color: #f1f2f6;
            padding: 12px;
            margin-bottom: 20px;
            font-size: 0.9em;
            border-radius: 4px;
        }
        .music-banner {
            background: #192a56;
            border: 2px solid #00d2d3;
            padding: 12px;
            border-radius: 8px;
            margin-bottom: 20px;
            text-align: center;
        }
        .music-btn {
            background: #00d2d3;
            color: #12181b;
            padding: 10px 20px;
            border: none;
            border-radius: 6px;
            font-weight: bold;
            cursor: pointer;
            font-size: 1em;
        }
        .music-btn:hover { background: #48dbfb; }
        .form-group { margin-bottom: 15px; }
        label { display: block; font-weight: bold; margin-bottom: 5px; color: #00d2d3; }
        input, select {
            width: 100%;
            padding: 12px;
            border: 1px solid #487eb0;
            border-radius: 6px;
            box-sizing: border-box;
            font-size: 1em;
            background-color: #2f3640;
            color: #f1f2f6;
        }
        input:focus, select:focus {
            outline: none;
            border-color: #00d2d3;
        }
        button.action-btn {
            background: #00b894;
            color: #12181b;
            padding: 14px;
            border: none;
            border-radius: 8px;
            font-weight: bold;
            width: 100%;
            cursor: pointer;
            font-size: 1.1em;
            margin-top: 15px;
            transition: background 0.2s;
        }
        button.action-btn:hover { background: #55efc4; }
        .result-card {
            background: #2f3640;
            border: 2px solid #00d2d3;
            padding: 20px;
            margin-top: 25px;
            border-radius: 8px;
            display: none;
        }
        .menu-box {
            background: #1e272c;
            padding: 15px;
            border-radius: 6px;
            border: 1px solid #487eb0;
            margin-top: 12px;
            line-height: 1.6;
            color: #f1f2f6;
        }
        .apa-container {
            margin-top: 30px;
            border: 1px solid #487eb0;
            border-radius: 8px;
            background: #1e272c;
            overflow: hidden;
        }
        .apa-header {
            background: #2f3640;
            color: #00d2d3;
            padding: 12px 15px;
            cursor: pointer;
            font-weight: bold;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        .apa-header:hover { background: #353b48; }
        .apa-content {
            padding: 15px;
            display: none;
            font-size: 0.88em;
            line-height: 1.6;
            background: #192227;
            color: #a4b0be;
        }
        .apa-content strong { color: #f1f2f6; }
    
        /* Paletas de Colores */
        body.theme-light { background-color: #f5f6fa !important; color: #2f3640 !important; }
        body.theme-light .container { background: #ffffff !important; border-color: #dcdde1 !important; box-shadow: 0 8px 24px rgba(0,0,0,0.1) !important; }
        body.theme-light h1, body.theme-light label, body.theme-light .apa-header, body.theme-light h3, body.theme-light h4 { color: #0097e6 !important; }
        body.theme-light input, body.theme-light select, body.theme-light textarea { background-color: #f1f2f6 !important; border-color: #718093 !important; color: #2f3640 !important; }
        body.theme-light .apa-content, body.theme-light .menu-box { background: #f8f9fa !important; color: #2f3640 !important; }

        body.theme-mint { background-color: #e8f8f5 !important; color: #117a65 !important; }
        body.theme-mint .container { background: #ffffff !important; border-color: #a3e4d7 !important; }
        body.theme-mint h1, body.theme-mint label, body.theme-mint .apa-header, body.theme-mint h3, body.theme-mint h4 { color: #16a085 !important; }
        body.theme-mint input, body.theme-mint select, body.theme-mint textarea { background-color: #e8f8f5 !important; border-color: #48c9b0 !important; color: #0e6251 !important; }

        body.theme-solar { background-color: #fef9e7 !important; color: #7d6608 !important; }
        body.theme-solar .container { background: #ffffff !important; border-color: #f9e79f !important; }
        body.theme-solar h1, body.theme-solar label, body.theme-solar .apa-header, body.theme-solar h3, body.theme-solar h4 { color: #d4ac0d !important; }
        body.theme-solar input, body.theme-solar select, body.theme-solar textarea { background-color: #fef9e7 !important; border-color: #f4d03f !important; color: #7d6608 !important; }

        body.theme-sky { background-color: #ebf5fb !important; color: #1b4f72 !important; }
        body.theme-sky .container { background: #ffffff !important; border-color: #aed6f1 !important; }
        body.theme-sky h1, body.theme-sky label, body.theme-sky .apa-header, body.theme-sky h3, body.theme-sky h4 { color: #2980b9 !important; }
        body.theme-sky input, body.theme-sky select, body.theme-sky textarea { background-color: #ebf5fb !important; border-color: #5dade2 !important; color: #154360 !important; }
    </style>
</head>
<body>

<div class="container">
    <!-- Selector de Paleta de Colores e Idioma -->
    <div class="top-bar">
        <div style="display: flex; align-items: center; gap: 8px;">
            <label for="paletteSelect" id="lblPalette" style="margin: 0; font-size: 0.9em;">🎨 Paleta:</label>
            <select id="paletteSelect" class="lang-box" onchange="changePalette()">
                <option value="" selected>Dark Cyber (Turquesa Oscuro)</option>
                <option value="theme-light">Light Clean (Claro Minimalista)</option>
                <option value="theme-mint">Mint Fresh (Menta Luminosa)</option>
                <option value="theme-solar">Solar Warmth (Sol Radiante)</option>
                <option value="theme-sky">Sky Breeze (Brisa Celeste)</option>
            </select>
        </div>
        <div style="display: flex; align-items: center; gap: 8px;">
            <label for="langSelect" id="lblLang" style="margin: 0; font-size: 0.9em;">Idioma:</label>
            <select id="langSelect" class="lang-box" onchange="changeLanguage()">
                <option value="es" selected>Español (predeterminado)</option>
                <option value="en">Ingles</option>
                <option value="de">Aleman</option>
                <option value="ru">Ruso</option>
                <option value="zh">Chino</option>
                <option value="ja">Japones</option>
                <option value="pt">Portugues</option>
                <option value="fr">Frances</option>
                <option value="it">Italiano</option>
                <option value="pl">Polaco</option>
            </select>
        </div>
    </div>

    <!-- WIDGET TIPO DE CAMBIO EN VIVO (USD A MXN) -->
    <div id="currencyWidget" style="background: #2f3640; border: 1px solid #00d2d3; padding: 10px 15px; border-radius: 6px; margin-bottom: 15px; display: flex; justify-content: space-between; align-items: center; font-size: 0.9em; flex-wrap: wrap; gap: 8px;">
        <span>💱 <strong>Tipo de Cambio Diario (USD/MXN):</strong> <span id="usdRateVal">Cargando...</span></span>
        <span style="font-size: 0.8em; color: #a4b0be;" id="rateDateVal">Actualizado en tiempo real</span>
    </div>

    <!-- GATITO ANIMADO SUPERIOR -->
    <div class="cat-avatar-container">
        <svg class="animated-cat" viewBox="0 0 100 100" xmlns="http://www.w3.org/2000/svg">
            <path class="cat-tail" d="M 55 70 Q 75 75 80 50 Q 82 40 78 35" fill="none" stroke="#00d2d3" stroke-width="5" stroke-linecap="round"/>
            <ellipse cx="50" cy="62" rx="22" ry="20" fill="#2f3640" stroke="#00d2d3" stroke-width="2.5"/>
            <circle cx="50" cy="38" r="18" fill="#2f3640" stroke="#00d2d3" stroke-width="2.5"/>
            <polygon points="35,26 38,10 47,23" fill="#2f3640" stroke="#00d2d3" stroke-width="2.5" stroke-linejoin="round"/>
            <polygon points="38,22 40,14 44,21" fill="#ff4757" opacity="0.6"/>
            <polygon points="65,26 62,10 53,23" fill="#2f3640" stroke="#00d2d3" stroke-width="2.5" stroke-linejoin="round"/>
            <polygon points="62,22 60,14 56,21" fill="#ff4757" opacity="0.6"/>
            <g class="cat-blink">
                <circle cx="43" cy="36" r="2.5" fill="#55efc4"/>
                <circle cx="57" cy="36" r="2.5" fill="#55efc4"/>
            </g>
            <ellipse cx="50" cy="43" rx="3" ry="2" fill="#ff7675"/>
            <line x1="47" y1="44" x2="36" y2="46" stroke="#718093" stroke-width="1.5" stroke-linecap="round"/>
            <line x1="47" y1="46" x2="35" y2="50" stroke="#718093" stroke-width="1.5" stroke-linecap="round"/>
            <line x1="53" y1="44" x2="64" y2="46" stroke="#718093" stroke-width="1.5" stroke-linecap="round"/>
            <line x1="53" y1="46" x2="65" y2="50" stroke="#718093" stroke-width="1.5" stroke-linecap="round"/>
            <path class="cat-heart" d="M 50 78 C 50 75 44 68 39 73 C 35 77 41 83 50 89 C 59 83 65 77 61 73 C 56 68 50 75 50 78 Z" fill="#ff4757"/>
        </svg>
    </div>

    <h1 id="mainTitle">🐱⚡ Simulador Terapéutico del Tío Nepomuceno</h1>
    <div class="subtitle" id="mainSub">Versión 12 — Precisión Metabólica, Costos, Guía de Cuidados y Redes</div>
    
    <div class="disclaimer" id="disclaimerBox">
        <strong>⚠️ Aviso Clínico:</strong> Herramienta avanzada de apoyo nutricional y simulación metabólica. No sustituye la valoración veterinaria presencial.
    </div>

    <!-- MÚSICA AMBIENTAL TENUE -->
    <div class="music-banner">
        <h3 id="musicTitle" style="color: #f1f2f6; margin: 0 0 8px 0;">🎶 Música Ambiental Tenue (Spa Felino Suave)</h3>
        <p id="musicSub" style="font-size: 0.9em; margin: 0 0 10px 0; color: #dfe4ea;">Atmósfera sonora sutil, luminosa y relajante:</p>
        <button class="music-btn" id="musicToggleBtn" type="button" onclick="toggleAmbientMusic()">▶️ Activar Música Ambiental</button>
    </div>

    <div>
        <h2 id="sectionParamsTitle">Parámetros Clínicos y Metabólicos</h2>
        <p id="sectionParamsSub">Configura los datos avanzados de tu felino para un cálculo de precisión científica:</p>

        <div class="form-group">
            <label id="lblCatName">Nombre del Gato:</label>
            <input type="text" id="iName" value="Neo">
        </div>

        <div style="display: flex; gap: 10px;">
            <div class="form-group" style="flex: 1;">
                <label id="lblYears">Edad (Años):</label>
                <input type="number" id="iYears" min="0" value="3">
            </div>
            <div class="form-group" style="flex: 1;">
                <label id="lblMonths">Meses (0-11):</label>
                <input type="number" id="iMonths" min="0" max="11" value="0">
            </div>
            <div class="form-group" style="flex: 1;">
                <label id="lblWeight">Peso Actual (kg):</label>
                <input type="number" id="iWeight" step="0.1" min="0.5" max="15" value="4.5">
            </div>
        </div>

        <div style="display: flex; gap: 10px;">
            <div class="form-group" style="flex: 1;">
                <label id="lblActivity">Nivel de Actividad Física:</label>
                <select id="iActivity">
                    <option value="sedentary">Sedentario (Interior / Bajo gasto - Factor 30)</option>
                    <option value="moderate" selected>Juguetón / Actividad Moderada (Factor 35)</option>
                    <option value="active">Muy Activo / Alta Energía (Factor 40)</option>
                </select>
            </div>
            <div class="form-group" style="flex: 1;">
                <label id="lblProtein">Proteína Base / Preferencia:</label>
                <select id="iProtein">
                    <option value="Pollo o Pavo" selected>Pollo o Pavo (Estándar Digestible)</option>
                    <option value="Res o Cerdo Magro">Res o Cerdo Magro (Alta Densidad)</option>
                    <option value="Pescado Blanco / Salmón">Pescado Blanco / Salmón (Soporte dérmico)</option>
                </select>
            </div>
        </div>

        <div class="form-group">
            <label id="lblSymptom">Condición Clínica / Objetivo Terapéutico:</label>
            <select id="iSymptom">
                <option value="sano">Gato Sano / Mantenimiento General</option>
                <option value="diarrea">Diarrea / Sensibilidad Digestiva (Inflamación intestinal)</option>
                <option value="pelaje">Pelaje Opaco / Piel Seca (Déficit de ácidos grasos)</option>
                <option value="renal">Enfermedad Renal Crónica (ERC / Cuidado de riñones)</option>
                <option value="urinario">Problemas Urinarios / FLUTD (Cistitis, prevención cristales)</option>
                <option value="sobrepeso">Sobrepeso / Obesidad (Control calórico y saciedad)</option>
                <option value="senior">Gato Senior Avanzado (Soporte articular y digestivo)</option>
            </select>
        </div>

        <div class="form-group">
            <label id="lblFruit">Fruta o Fitonutriente Seguro (Aporte hídrico y antioxidante - Máx 3-5%):</label>
            <select id="iFruit">
                <option value="sin">🚫 Sin fruta (Excluir vegetales)</option>
                <option value="arandanos">Arándanos (Antioxidantes y tracto urinario)</option>
                <option value="manzana">Manzana sin semillas (Pectina digestiva)</option>
                <option value="sandia">Sandía en cubitos (Hidratación renal y urinaria)</option>
                <option value="melon">Melón (Aporte vitamínico suave)</option>
            </select>
        </div>

        <button class="action-btn" id="btnCalc" type="button" onclick="handleCalculateClick()">⚡ Calcular Simulación y Receta Avanzada</button>

        <div id="resultCard" class="result-card">
            <h3 id="resTitle" style="color: #00d2d3; margin-top: 0;"></h3>
            <p id="resPredx"></p>
            <p id="resPortion" style="font-weight: bold; color: #55efc4; font-size: 1.1em;"></p>
            <p id="resWater" style="font-weight: bold; color: #74b9ff; font-size: 1em;"></p>
            
            <div class="menu-box">
                <strong id="menuBoxTitle" style="color: #00d2d3;">🍲 Menú Terapéutico y Protocolo de Hidratación:</strong><br><br>
                <span id="resMenu"></span>
            </div>
            
            <div style="background: #192227; border: 2px dashed #00d2d3; padding: 15px; border-radius: 8px; margin-top: 20px; text-align: center;">
                <h4 id="exportTitle" style="color: #00d2d3; margin: 0 0 8px 0;">📥 Exportación Oficial</h4>
                <button class="action-btn" id="btnDownload" type="button" style="background: #0984e3; color: #fff; margin-top:0; width: auto; padding: 10px 25px;" onclick="handleDownloadClick()">📥 Descargar Reporte Clínico en PDF</button>
            </div>
        </div>
    </div>

    <!-- INFORMACIÓN QUE CURA Y GUÍA DE CUIDADOS -->
    <div style="margin-top: 25px; background: #192227; border: 2px solid #ff4757; padding: 20px; border-radius: 8px;">
        <h3 style="color: #ff4757; margin-top: 0;">💡 Información que Cura: Guía de Supervivencia y Cuidados Felinos</h3>
        
        <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(260px, 1fr)); gap: 15px; margin-top: 15px;">
            <div style="background: #2f3640; padding: 15px; border-radius: 6px; border: 1px solid #ff6b81;">
                <h4 style="margin-top: 0; color: #ff6b81;">🚫 Alimentos y Condimentos PROHIBIDOS</h4>
                <p style="font-size: 0.88em; line-height: 1.5; color: #f1f2f6; margin-bottom: 0;">
                    • <strong>Cebolla y Ajo:</strong> Destruyen los glóbulos rojos (anemia severa).<br>
                    • <strong>Chocolate, Café y Té:</strong> La teobromina y cafeína son altamente tóxicas.<br>
                    • <strong>Uvas y Pasas:</strong> Provocan fallo renal agudo en felinos.<br>
                    • <strong>Sal añadida y especias:</strong> Dañan los riñones a corto y mediano plazo.<br>
                    • <strong>Huesos cocidos:</strong> Se astillan y perforan el tracto digestivo.
                </p>
            </div>

            <div style="background: #2f3640; padding: 15px; border-radius: 6px; border: 1px solid #2ed573;">
                <h4 style="margin-top: 0; color: #2ed573;">🍏 Frutas Seguras vs. Tóxicas</h4>
                <p style="font-size: 0.88em; line-height: 1.5; color: #f1f2f6; margin-bottom: 0;">
                    ✅ <strong>Seguras (en porciones &lt; 5%):</strong> Arándanos, manzana sin semillas, trocitos de sandía y melón.<br>
                    ❌ <strong>Tóxicas / Prohibidas:</strong> Cítricos (limón, naranja, toronja - dañan su estómago), uvas, aguacate (la persina es tóxica).
                </p>
            </div>

            <div style="background: #2f3640; padding: 15px; border-radius: 6px; border: 1px solid #70a1ff;">
                <h4 style="margin-top: 0; color: #70a1ff;">🐈 Cuidados Esenciales (Pelaje, Uñas y Dental)</h4>
                <p style="font-size: 0.88em; line-height: 1.5; color: #f1f2f6; margin-bottom: 0;">
                    • <strong>Pelaje:</strong> Cepillado semanal (diario en razas de pelo largo) para evitar bolas de pelo (tricobezoares).<br>
                    • <strong>Uñas:</strong> Corte cuidadoso de la punta transparente cada 3-4 semanas y rascadores firmes.<br>
                    • <strong>Dental:</strong> Prevención de sarro con cepillado con pasta enzimática felina (¡nunca pasta de humanos!).
                </p>
            </div>
        </div>
    </div>

    <!-- MÓDULO AVANZADO: SUPLEMENTOS, HISTORIAL Y COSTOS REALES CON FRUTA -->
    <div style="margin-top: 25px; background: #1e272c; border: 1px solid #487eb0; padding: 20px; border-radius: 8px;">
        <h3 style="color: #00d2d3; margin-top: 0;">🔬 Suplementos Milimétricos, Presupuesto Real y Control de Peso</h3>
        
        <div style="display: flex; gap: 15px; flex-wrap: wrap; margin-bottom: 15px;">
            <div style="flex: 1; min-width: 250px; background: #2f3640; padding: 15px; border-radius: 6px; border: 1px solid #487eb0;">
                <h4 style="margin-top: 0; color: #55efc4;">💊 Dosificación Exacta de Suplementos</h4>
                <p id="suppDetailText" style="font-size: 0.9em; line-height: 1.5; color: #f1f2f6;">
                    Calculado según peso actual: Taurina (250mg/día), Carbonato de Calcio y Aceite de Salmón.
                </p>
                <div id="suppCalcResult" style="font-weight: bold; color: #00d2d3; margin-top: 8px;"></div>
            </div>

            <div style="flex: 1; min-width: 250px; background: #2f3640; padding: 15px; border-radius: 6px; border: 1px solid #487eb0;">
                <h4 style="margin-top: 0; color: #74b9ff;">⏰ Alarma y Temporizador de Comida</h4>
                <p style="font-size: 0.9em; margin-bottom: 8px;">Programa el próximo horario de alimentación:</p>
                <div style="display: flex; gap: 8px; margin-bottom: 8px;">
                    <input type="time" id="mealTimerInput" class="lang-box" style="flex: 1;">
                    <button class="music-btn" type="button" onclick="setMealAlarm()" style="padding: 8px 12px; font-size: 0.9em;">🔔 Activar</button>
                </div>
                <div id="alarmStatus" style="font-size: 0.85em; color: #55efc4;"></div>
            </div>
        </div>

        <div style="display: flex; gap: 15px; flex-wrap: wrap; margin-bottom: 15px;">
            <!-- Calculadora de Presupuesto Real -->
            <div style="flex: 1; min-width: 280px; background: #2f3640; padding: 15px; border-radius: 6px; border: 1px solid #487eb0;">
                <h4 style="margin-top: 0; color: #f39c12;">💰 Calculadora de Presupuesto Real (Carne + Fruta)</h4>
                <div style="display: flex; gap: 10px; margin-bottom: 8px;">
                    <div style="flex: 1;">
                        <label style="font-size: 0.85em; color: #a4b0be;">Costo Carne / kg ($):</label>
                        <input type="number" id="costPerKgInput" value="120" step="1" oninput="calculateMonthlyCost()" style="padding: 8px; font-size: 0.9em;">
                    </div>
                    <div style="flex: 1;">
                        <label style="font-size: 0.85em; color: #a4b0be;">Costo Fruta / kg ($):</label>
                        <input type="number" id="costFruitPerKgInput" value="50" step="1" oninput="calculateMonthlyCost()" style="padding: 8px; font-size: 0.9em;">
                    </div>
                </div>
                <div id="monthlyCostResult" style="font-size: 0.9em; font-weight: bold; color: #00d2d3; margin-top: 6px;">Gasto estimado mensual: --</div>
                <div id="usdConversionResult" style="font-size: 0.85em; color: #74b9ff; margin-top: 4px;">Equivalente en USD: --</div>
            </div>

            <!-- Inventario de Despensa -->
            <div style="flex: 1; min-width: 250px; background: #2f3640; padding: 15px; border-radius: 6px; border: 1px solid #487eb0;">
                <h4 style="margin-top: 0; color: #f39c12;">📦 Inventario de Despensa</h4>
                <p style="font-size: 0.88em; margin-bottom: 8px;">Stock actual de proteína (kg):</p>
                <div style="display: flex; gap: 8px;">
                    <input type="number" id="pantryStockInput" placeholder="Ej. 2.5 kg" step="0.1" style="padding: 6px; font-size: 0.9em; flex: 1;">
                    <button class="music-btn" type="button" onclick="updatePantry()" style="padding: 6px 10px; font-size: 0.85em;">Actualizar</button>
                </div>
                <div id="pantryStatus" style="font-size: 0.85em; color: #f1f2f6; margin-top: 6px;">Stock registrado: Ninguno</div>
            </div>
        </div>

        <div style="background: #2f3640; padding: 15px; border-radius: 6px; border: 1px solid #487eb0;">
            <h4 style="margin-top: 0; color: #00d2d3;">📈 Historial de Evolución de Peso (Guardado Local)</h4>
            <div style="display: flex; gap: 10px; align-items: center; margin-bottom: 10px;">
                <input type="number" id="historyWeightInput" step="0.1" placeholder="Peso registrado (kg)" style="max-width: 180px; padding: 8px;">
                <button class="music-btn" type="button" onclick="saveWeightHistory()" style="padding: 8px 15px; font-size: 0.9em;">➕ Guardar Registro</button>
                <button class="music-btn" type="button" onclick="clearWeightHistory()" style="background: #ff4757; color: #fff; padding: 8px 15px; font-size: 0.9em;">🗑️ Limpiar Historial</button>
            </div>
            <div id="weightHistoryList" style="font-size: 0.9em; max-height: 100px; overflow-y: auto; background: #192227; padding: 8px; border-radius: 4px;"></div>
        </div>
    </div>

    <!-- MÓDULOS MAESTROS: MULTI-GATO, TRANSICIÓN Y NOTAS CLÍNICAS -->
    <div style="margin-top: 25px; background: #1e272c; border: 1px solid #487eb0; padding: 20px; border-radius: 8px;">
        <h3 style="color: #00d2d3; margin-top: 0;">🌟 Módulos Maestros de Responsabilidad Felina</h3>
        
        <div style="display: flex; gap: 15px; flex-wrap: wrap; margin-top: 15px;">
            <div style="flex: 1; min-width: 260px; background: #2f3640; padding: 15px; border-radius: 6px; border: 1px solid #487eb0;">
                <h4 style="margin-top: 0; color: #55efc4;">🐱 Perfiles Multi-Gato</h4>
                <p style="font-size: 0.88em; margin-bottom: 8px;">Guarda y cambia entre los perfiles de tus felinos:</p>
                <div style="display: flex; gap: 8px;">
                    <button class="music-btn" type="button" onclick="saveCatProfile()" style="padding: 6px 10px; font-size: 0.85em; flex: 1;">💾 Guardar Perfil</button>
                    <select id="profileSelect" class="lang-box" onchange="loadCatProfile()" style="flex: 1; padding: 6px;"></select>
                </div>
            </div>

            <div style="flex: 1; min-width: 260px; background: #2f3640; padding: 15px; border-radius: 6px; border: 1px solid #487eb0;">
                <h4 style="margin-top: 0; color: #00d2d3;">🩺 Notas Clínicas del Veterinario</h4>
                <textarea id="vetNotesInput" placeholder="Indicaciones específicas del médico veterinario..." style="width: 100%; height: 45px; background: #192227; color: #f1f2f6; border: 1px solid #487eb0; border-radius: 4px; padding: 6px; font-size: 0.85em; box-sizing: border-box;"></textarea>
            </div>
        </div>

        <div style="margin-top: 15px; background: #2f3640; padding: 15px; border-radius: 6px; border: 1px solid #487eb0;">
            <h4 style="margin-top: 0; color: #00d2d3;">🔄 Planificador de Transición Dietética Gradual (7 Días)</h4>
            <div style="display: grid; grid-template-columns: repeat(4, 1fr); gap: 6px; text-align: center; font-size: 0.8em; margin-top: 8px;">
                <div style="background: #192227; padding: 6px; border-radius: 4px;"><strong>Día 1-2</strong><br>25% Natural<br>75% Anterior</div>
                <div style="background: #192227; padding: 6px; border-radius: 4px;"><strong>Día 3-4</strong><br>50% Natural<br>50% Anterior</div>
                <div style="background: #192227; padding: 6px; border-radius: 4px;"><strong>Día 5-6</strong><br>75% Natural<br>25% Anterior</div>
                <div style="background: #192227; padding: 6px; border-radius: 4px; border: 1px solid #00d2d3;"><strong>Día 7+</strong><br>100% Dieta Natural 🐾</div>
            </div>
        </div>
    </div>

    <!-- MÓDULO DE COMPARTIR EN REDES -->
    <div style="margin-top: 25px; background: #1e272c; border: 2px solid #00d2d3; padding: 20px; border-radius: 8px; text-align: center;">
        <h3 style="color: #00d2d3; margin-top: 0;">🌐 ¡Comparte el Simulador con el Mundo!</h3>
        <p style="font-size: 0.9em; color: #a4b0be; margin-bottom: 15px;">Ayuda a más papás y mamás de gatos a calcular porciones exactas y cuidar a sus michis con bases científicas.</p>
        <div style="display: flex; gap: 10px; justify-content: center; flex-wrap: wrap;">
            <button class="action-btn" type="button" style="background: #25D366; color: #fff; margin-top:0; width: auto; padding: 12px 20px; font-size: 0.95em;" onclick="shareOnWhatsApp()">💬 Compartir en WhatsApp</button>
            <button class="action-btn" type="button" style="background: #0984e3; color: #fff; margin-top:0; width: auto; padding: 12px 20px; font-size: 0.95em;" onclick="copyShareLink()">🔗 Copiar Enlace</button>
        </div>
    </div>

    <!-- RECUADRO APA INTERACTIVO -->
    <div class="apa-container">
        <div class="apa-header" onclick="toggleApaContent()">
            <span id="apaHeaderTxt">📚 Referencias Científicas Avanzadas (Formato APA) — Ver Respaldos Clínicos</span>
            <span id="apaArrow">▼</span>
        </div>
        <div class="apa-content" id="apaContentBox">
            <p><strong>1. Fundamentos de Nutrición y Requerimientos Energéticos:</strong> Case, L. P., et al. (2011). <em>Canine and Feline Nutrition</em>. Mosby Elsevier.</p>
            <p><strong>2. Directrices de Hidratación y Manejo Renal:</strong> FEDIAF. (2021). <em>Nutritional Guidelines for Complete and Complementary Pet Food</em>.</p>
            <p><strong>3. Perfil Aminoacídico y Selección de Proteínas:</strong> NRC. (2006). <em>Nutrient Requirements of Dogs and Cats</em>. National Academies Press.</p>
            <p><strong>4. Fisiología Digestiva y Tolerancia a Fitonutrientes:</strong> Zoran, L. D. (2002). The carnivore connection to nutrition in cats. <em>JAVMA</em>.</p>
        </div>
    </div>

</div>

<script>
    let currentSimulation = {};
    let ambientCtx = null;
    let ambientInterval = null;
    let isMusicPlaying = false;
    let currentUsdToMxn = 18.67;

    async function fetchExchangeRate() {
        try {
            const res = await fetch('https://open.er-api.com/v6/latest/USD');
            const data = await res.json();
            if(data && data.rates && data.rates.MXN) {
                currentUsdToMxn = data.rates.MXN;
                document.getElementById('usdRateVal').innerText = `$${currentUsdToMxn.toFixed(2)} MXN / USD`;
                document.getElementById('rateDateVal').innerText = `Actualizado hoy (${new Date().toLocaleDateString()})`;
                calculateMonthlyCost();
            }
        } catch(e) {
            document.getElementById('usdRateVal').innerText = `$18.67 MXN / USD (Referencia)`;
        }
    }
    window.addEventListener('DOMContentLoaded', fetchExchangeRate);

    const dict = {
        es: {
            lblPalette: "🎨 Paleta:",
            lblLang: "Idioma:",
            mainTitle: "🐱⚡ Simulador Terapéutico del Tío Nepomuceno",
            mainSub: "Versión 12 — Precisión Metabólica, Costos, Guía de Cuidados y Redes",
            disclaimer: "⚠️ Aviso Clínico: Herramienta avanzada de apoyo nutricional y simulación metabólica. No sustituye la valoración veterinaria presencial."
        }
    };

    function changePalette() {
        playHappyMeow();
        document.body.className = document.getElementById('paletteSelect').value;
    }

    function changeLanguage() {
        const d = dict['es'];
        document.getElementById('lblLang').innerText = d.lblLang;
        document.getElementById('mainTitle').innerText = d.mainTitle;
        document.getElementById('mainSub').innerText = d.mainSub;
    }

    function toggleAmbientMusic() {
        const btn = document.getElementById('musicToggleBtn');
        if (!isMusicPlaying) {
            try {
                ambientCtx = new (window.AudioContext || window.webkitAudioContext)();
                if (ambientCtx.state === 'suspended') ambientCtx.resume();
                const chords = [[523.25, 659.25, 783.99], [440.00, 523.25, 659.25], [349.23, 440.00, 523.25], [392.00, 493.88, 587.33]];
                let chordIndex = 0;
                function playChord() {
                    if (!isMusicPlaying || !ambientCtx) return;
                    const notes = chords[chordIndex];
                    chordIndex = (chordIndex + 1) % chords.length;
                    notes.forEach((freq) => {
                        try {
                            const osc = ambientCtx.createOscillator();
                            const gain = ambientCtx.createGain();
                            const filter = ambientCtx.createBiquadFilter();
                            osc.type = 'sine';
                            osc.frequency.setValueAtTime(freq, ambientCtx.currentTime);
                            filter.type = 'lowpass';
                            filter.frequency.setValueAtTime(300, ambientCtx.currentTime);
                            gain.gain.setValueAtTime(0.0001, ambientCtx.currentTime);
                            gain.gain.linearRampToValueAtTime(0.008, ambientCtx.currentTime + 2.0);
                            gain.gain.exponentialRampToValueAtTime(0.0001, ambientCtx.currentTime + 5.5);
                            osc.connect(filter);
                            filter.connect(gain);
                            gain.connect(ambientCtx.destination);
                            osc.start(ambientCtx.currentTime);
                            osc.stop(ambientCtx.currentTime + 6.0);
                        } catch(err) {}
                    });
                }
                playChord();
                ambientInterval = setInterval(playChord, 5500);
                isMusicPlaying = true;
                btn.innerText = "⏸️ Pausar Música Ambiental";
                btn.style.background = "#ff4757";
            } catch (e) {}
        } else {
            if (ambientInterval) clearInterval(ambientInterval);
            if (ambientCtx) { ambientCtx.close(); ambientCtx = null; }
            isMusicPlaying = false;
            btn.innerText = "▶️ Activar Música Ambiental";
            btn.style.background = "#00d2d3";
        }
    }

    function playHappyMeow() {
        try {
            const ctx = new (window.AudioContext || window.webkitAudioContext)();
            const osc = ctx.createOscillator();
            const gain = ctx.createGain();
            osc.type = 'sine';
            const now = ctx.currentTime;
            osc.frequency.setValueAtTime(450, now);
            osc.frequency.exponentialRampToValueAtTime(800, now + 0.12);
            osc.frequency.exponentialRampToValueAtTime(550, now + 0.3);
            gain.gain.setValueAtTime(0, now);
            gain.gain.linearRampToValueAtTime(0.12, now + 0.04);
            gain.gain.exponentialRampToValueAtTime(0.01, now + 0.35);
            osc.connect(gain);
            gain.connect(ctx.destination);
            osc.start(now);
            osc.stop(now + 0.35);
        } catch(e) {}
    }

    function toggleApaContent() {
        playHappyMeow();
        const box = document.getElementById('apaContentBox');
        const arrow = document.getElementById('apaArrow');
        if (box.style.display === 'block') { box.style.display = 'none'; arrow.innerText = '▼'; }
        else { box.style.display = 'block'; arrow.innerText = '▲'; }
    }

    function handleCalculateClick() {
        playHappyMeow();
        generateInteractiveDiet();
    }

    function handleDownloadClick() {
        playHappyMeow();
        downloadSimulationPDF();
    }

    function shareOnWhatsApp() {
        playHappyMeow();
        const text = encodeURIComponent("¡Hola! Te comparto el Simulador Terapéutico del Tío Nepomuceno 🐱, una herramienta web excelente para calcular porciones de comida, hidratación y cuidados felinos con base científica. Pruébalo aquí: " + window.location.href);
        window.open("https://api.whatsapp.com/send?text=" + text, "_blank");
    }

    function copyShareLink() {
        playHappyMeow();
        navigator.clipboard.writeText(window.location.href).then(() => {
            alert("¡Enlace copiado al portapapeles! 📋 Ya puedes pegarlo en tus redes sociales o grupos.");
        }).catch(err => {
            alert("No se pudo copiar el enlace automáticamente.");
        });
    }

    function updateSupplements(weight) {
        let taurineMg = 250;
        let calciumG = (weight * 0.1).toFixed(2);
        let salmonDrops = Math.round(weight * 3);
        document.getElementById('suppCalcResult').innerHTML = 
            `• Taurina: <strong>${taurineMg} mg</strong><br>` +
            `• Carbonato de Calcio: <strong>${calciumG}g</strong><br>` +
            `• Aceite de Salmón: <strong>${salmonDrops} gotas</strong>`;
    }

    function saveWeightHistory() {
        playHappyMeow();
        const w = parseFloat(document.getElementById('historyWeightInput').value);
        if(!w || isNaN(w)) { alert('Ingresa un peso válido.'); return; }
        let history = JSON.parse(localStorage.getItem('cat_weight_history_v12') || '[]');
        history.push({date: new Date().toLocaleDateString(), weight: w});
        localStorage.setItem('cat_weight_history_v12', JSON.stringify(history));
        renderWeightHistory();
        document.getElementById('historyWeightInput').value = '';
    }

    function clearWeightHistory() {
        playHappyMeow();
        if(confirm('¿Deseas borrar el historial?')) {
            localStorage.removeItem('cat_weight_history_v12');
            renderWeightHistory();
        }
    }

    function renderWeightHistory() {
        let history = JSON.parse(localStorage.getItem('cat_weight_history_v12') || '[]');
        const container = document.getElementById('weightHistoryList');
        if(history.length === 0) { container.innerHTML = '<span style="color: #718093;">Sin registros de peso.</span>'; return; }
        let html = '<ul style="margin: 0; padding-left: 20px;">';
        history.forEach(item => { html += `<li>📅 ${item.date} — <strong>${item.weight} kg</strong></li>`; });
        html += '</ul>';
        container.innerHTML = html;
    }

    let alarmInterval = null;
    function setMealAlarm() {
        playHappyMeow();
        const timeVal = document.getElementById('mealTimerInput').value;
        if(!timeVal) { alert('Selecciona una hora.'); return; }
        const now = new Date();
        const [hours, minutes] = timeVal.split(':');
        let target = new Date();
        target.setHours(parseInt(hours), parseInt(minutes), 0);
        if(target <= now) target.setDate(target.getDate() + 1);
        let targetTimeMs = target.getTime();
        if(alarmInterval) clearInterval(alarmInterval);
        alarmInterval = setInterval(() => {
            const diffMs = targetTimeMs - new Date().getTime();
            if(diffMs <= 0) {
                clearInterval(alarmInterval);
                playHappyMeow();
                document.getElementById('alarmStatus').innerHTML = '⏰ ¡Hora de alimentar a tu felino!';
                alert('⏰ ¡Hora de alimentar a tu felino!');
            } else {
                const totalSecs = Math.floor(diffMs / 1000);
                const hrs = Math.floor(totalSecs / 3600);
                const mins = Math.floor((totalSecs % 3600) / 60);
                const secs = totalSecs % 60;
                document.getElementById('alarmStatus').innerHTML = `⏳ <strong>${hrs}h ${mins}m ${secs}s</strong>`;
            }
        }, 1000);
        document.getElementById('alarmStatus').innerText = `✅ Alarma lista para las ${timeVal}`;
    }
    window.addEventListener('DOMContentLoaded', renderWeightHistory);

    function saveCatProfile() {
        playHappyMeow();
        const name = document.getElementById('iName').value.trim() || "Neo";
        const profile = {
            name, years: document.getElementById('iYears').value, months: document.getElementById('iMonths').value,
            weight: document.getElementById('iWeight').value, activity: document.getElementById('iActivity').value,
            protein: document.getElementById('iProtein').value, symptom: document.getElementById('iSymptom').value,
            fruit: document.getElementById('iFruit').value
        };
        let profiles = JSON.parse(localStorage.getItem('cat_profiles_v12') || '{}');
        profiles[name] = profile;
        localStorage.setItem('cat_profiles_v12', JSON.stringify(profiles));
        updateProfileSelect();
        alert(`¡Perfil de ${name} guardado!`);
    }

    function updateProfileSelect() {
        let profiles = JSON.parse(localStorage.getItem('cat_profiles_v12') || '{}');
        const sel = document.getElementById('profileSelect');
        sel.innerHTML = '<option value="">Cargar perfil...</option>';
        for(let key in profiles) sel.innerHTML += `<option value="${key}">${key}</option>`;
    }

    function loadCatProfile() {
        playHappyMeow();
        const name = document.getElementById('profileSelect').value;
        if(!name) return;
        let profiles = JSON.parse(localStorage.getItem('cat_profiles_v12') || '{}');
        const p = profiles[name];
        if(p) {
            document.getElementById('iName').value = p.name;
            document.getElementById('iYears').value = p.years;
            document.getElementById('iMonths').value = p.months;
            document.getElementById('iWeight').value = p.weight;
            document.getElementById('iActivity').value = p.activity;
            document.getElementById('iProtein').value = p.protein;
            document.getElementById('iSymptom').value = p.symptom;
            document.getElementById('iFruit').value = p.fruit;
            generateInteractiveDiet();
        }
    }
    window.addEventListener('DOMContentLoaded', updateProfileSelect);

    function calculateMonthlyCost() {
        const weight = parseFloat(document.getElementById('iWeight').value) || 4.0;
        const activity = document.getElementById('iActivity').value;
        let factor = activity === 'sedentary' ? 30 : (activity === 'active' ? 40 : 35);
        let totalDailyGrams = Math.min(350, Math.max(90, Math.round(weight * factor)));

        const fruit = document.getElementById('iFruit').value;
        let meatGrams = fruit === 'sin' ? Math.round(totalDailyGrams * 0.86) : Math.round(totalDailyGrams * 0.82);
        let fruitGrams = fruit === 'sin' ? 0 : Math.round(totalDailyGrams * 0.04);
        let organGrams = Math.round(totalDailyGrams * 0.10);

        const costMeatPerKg = parseFloat(document.getElementById('costPerKgInput').value) || 120;
        const costFruitPerKg = parseFloat(document.getElementById('costFruitPerKgInput').value) || 50;

        const monthlyMeatKg = ((meatGrams + organGrams) * 30) / 1000;
        const monthlyFruitKg = (fruitGrams * 30) / 1000;

        const monthlyCostMxn = Math.round((monthlyMeatKg * costMeatPerKg) + (monthlyFruitKg * costFruitPerKg));
        const monthlyCostUsd = (monthlyCostMxn / currentUsdToMxn).toFixed(2);

        document.getElementById('monthlyCostResult').innerText = `Gasto estimado mensual: ~$${monthlyCostMxn} MXN`;
        document.getElementById('usdConversionResult').innerText = `Equivalente en USD: ~$${monthlyCostUsd} USD (TC: ${currentUsdToMxn.toFixed(2)})`;
    }

    function updatePantry() {
        playHappyMeow();
        const stock = document.getElementById('pantryStockInput').value;
        if(!stock) return;
        localStorage.setItem('cat_pantry_stock_v12', stock);
        document.getElementById('pantryStatus').innerText = `Stock registrado: ${stock} kg`;
        document.getElementById('pantryStockInput').value = '';
    }
    window.addEventListener('DOMContentLoaded', () => {
        const s = localStorage.getItem('cat_pantry_stock_v12');
        if(s) document.getElementById('pantryStatus').innerText = `Stock registrado: ${s} kg`;
    });

    function generateInteractiveDiet() {
        const name = document.getElementById('iName').value.trim() || "Neo";
        const years = parseInt(document.getElementById('iYears').value) || 0;
        const months = parseInt(document.getElementById('iMonths').value) || 0;
        const weight = parseFloat(document.getElementById('iWeight').value) || 4.0;
        const activity = document.getElementById('iActivity').value;
        const protein = document.getElementById('iProtein').value;
        const symptom = document.getElementById('iSymptom').value;
        const fruit = document.getElementById('iFruit').value;

        let factor = activity === 'sedentary' ? 30 : (activity === 'active' ? 40 : 35);
        let totalDailyGrams = Math.min(350, Math.max(90, Math.round(weight * factor)));
        let totalWaterMl = Math.round(weight * 52);

        let totalSpoons = (totalDailyGrams / 15).toFixed(1);
        let waterSpoons = (totalWaterMl / 15).toFixed(1);

        let meatGrams, organGrams, fruitGrams, supplementGrams, specificMenu;

        if(fruit === 'sin') {
            meatGrams = Math.round(totalDailyGrams * 0.86);
            organGrams = Math.round(totalDailyGrams * 0.10);
            fruitGrams = 0;
            supplementGrams = totalDailyGrams - (meatGrams + organGrams);
            specificMenu = `• <strong>Proteína base (${protein}):</strong> ${meatGrams}g (${(meatGrams/15).toFixed(1)} cdas).<br>` +
                           `• <strong>Vísceras esenciales:</strong> ${organGrams}g (${(organGrams/15).toFixed(1)} cdas).<br>` +
                           `• <strong>Fitonutrientes:</strong> Excluidos.<br>` +
                           `• <strong>Suplementos (Calcio/Taurina):</strong> ${supplementGrams}g.`;
        } else {
            meatGrams = Math.round(totalDailyGrams * 0.82);
            organGrams = Math.round(totalDailyGrams * 0.10);
            fruitGrams = Math.round(totalDailyGrams * 0.04);
            supplementGrams = totalDailyGrams - (meatGrams + organGrams + fruitGrams);
            specificMenu = `• <strong>Proteína base (${protein}):</strong> ${meatGrams}g (${(meatGrams/15).toFixed(1)} cdas).<br>` +
                           `• <strong>Vísceras esenciales:</strong> ${organGrams}g (${(organGrams/15).toFixed(1)} cdas).<br>` +
                           `• <strong>Fruta (${fruit}):</strong> ${fruitGrams}g (${(fruitGrams/15).toFixed(1)} cdas).<br>` +
                           `• <strong>Suplementos (Calcio/Taurina):</strong> ${supplementGrams}g.`;
        }

        currentSimulation = {
            date: new Date().toLocaleDateString() + ' ' + new Date().toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'}),
            name, years, months, weight, activity, protein, symptom, fruit, totalDailyGrams, totalWaterMl, meatGrams, organGrams, fruitGrams, supplementGrams, specificMenu
        };

        updateSupplements(weight);
        calculateMonthlyCost();
        document.getElementById('resTitle').innerText = `📋 Reporte Clínico y Nutricional para: ${name}`;
        document.getElementById('resPredx').innerHTML = `<strong>Edad:</strong> ${years} años y ${months} meses | <strong>Peso:</strong> ${weight} kg<br>Actividad: ${activity} | Condición: ${symptom}`;
        document.getElementById('resPortion').innerHTML = `⚖️ Porción Diaria Total: <u>${totalDailyGrams} gramos</u> (~${totalSpoons} cdas/día)`;
        document.getElementById('resWater').innerHTML = `💧 Hidratación Sugerida: <u>${totalWaterMl} ml / día</u> (~${waterSpoons} cdas)`;
        document.getElementById('resMenu').innerHTML = specificMenu;
        
        const card = document.getElementById('resultCard');
        card.style.display = 'block';
        card.scrollIntoView({ behavior: 'smooth' });
    }

    function generatePDFBlob(sim) {
        const { jsPDF } = window.jspdf;
        const doc = new jsPDF();
        const palette = document.body.className;
        let bgCol = [26, 34, 39], boxCol = [31, 43, 51], subBoxCol = [39, 48, 58], accentCol = [0, 210, 211], textCol = [241, 242, 246], mutedCol = [164, 176, 190], borderCol = [0, 210, 211];

        if (palette === 'theme-light') { bgCol = [245, 246, 250]; boxCol = [255, 255, 255]; subBoxCol = [241, 242, 246]; accentCol = [0, 151, 230]; textCol = [47, 54, 64]; mutedCol = [113, 128, 147]; borderCol = [220, 221, 225]; }
        else if (palette === 'theme-mint') { bgCol = [232, 248, 245]; boxCol = [255, 255, 255]; subBoxCol = [240, 250, 248]; accentCol = [22, 160, 133]; textCol = [17, 122, 101]; mutedCol = [72, 201, 176]; borderCol = [163, 228, 215]; }
        else if (palette === 'theme-solar') { bgCol = [254, 249, 231]; boxCol = [255, 255, 255]; subBoxCol = [254, 252, 243]; accentCol = [212, 172, 13]; textCol = [125, 102, 8]; mutedCol = [180, 150, 20]; borderCol = [249, 231, 159]; }
        else if (palette === 'theme-sky') { bgCol = [235, 245, 251]; boxCol = [255, 255, 255]; subBoxCol = [245, 250, 254]; accentCol = [41, 128, 185]; textCol = [27, 79, 114]; mutedCol = [93, 173, 226]; borderCol = [174, 214, 241]; }

        doc.setFillColor(bgCol[0], bgCol[1], bgCol[2]);
        doc.rect(0, 0, 210, 297, "F");
        doc.setLineWidth(0.8); doc.setDrawColor(borderCol[0], borderCol[1], borderCol[2]); doc.rect(8, 8, 194, 281);

        doc.setFillColor(boxCol[0], boxCol[1], boxCol[2]); doc.rect(8, 8, 194, 24, "F");
        doc.setTextColor(accentCol[0], accentCol[1], accentCol[2]); doc.setFont("helvetica", "bold"); doc.setFontSize(10);
        doc.text("SIMULADOR TERAPEUTICO DEL TIO NEPOMUCENO — REPORTE v12", 105, 23, { align: "center" });

        doc.setTextColor(textCol[0], textCol[1], textCol[2]); doc.setFontSize(14);
        doc.text("SIMULACION METABOLICA AVANZADA Y COSTOS", 105, 42, { align: "center" });

        doc.setFillColor(subBoxCol[0], subBoxCol[1], subBoxCol[2]); doc.roundedRect(15, 50, 180, 44, 3, 3, "FD");
        doc.setFont("helvetica", "bold"); doc.setFontSize(10); doc.setTextColor(accentCol[0], accentCol[1], accentCol[2]);
        doc.text("DATOS DE LA CONSULTA Y GASTO ENERGETICO", 20, 58);
        doc.setFont("helvetica", "normal"); doc.setFontSize(9); doc.setTextColor(textCol[0], textCol[1], textCol[2]);
        doc.text("- Fecha: " + sim.date, 20, 66);
        doc.text("- Felino: " + sim.name + " (" + sim.years + "a " + sim.months + "m) | Peso: " + sim.weight + " kg", 20, 73);
        doc.text("- Actividad: " + sim.activity + " | Condición: " + sim.symptom, 20, 80);
        doc.text("- Proteina Base: " + sim.protein, 20, 87);

        doc.setFillColor(subBoxCol[0], subBoxCol[1], subBoxCol[2]); doc.roundedRect(15, 98, 180, 18, 2, 2, "FD");
        doc.setFont("helvetica", "bold"); doc.setFontSize(10); doc.setTextColor(accentCol[0], accentCol[1], accentCol[2]);
        doc.text("Porcion: " + sim.totalDailyGrams + " g/dia", 20, 109);
        doc.text("Hidratacion: " + sim.totalWaterMl + " ml/dia", 110, 109);

        doc.setFont("helvetica", "bold"); doc.setFontSize(11); doc.setTextColor(textCol[0], textCol[1], textCol[2]);
        doc.text("Menu Terapéutico y Porciones:", 15, 126);

        doc.setFillColor(boxCol[0], boxCol[1], boxCol[2]); doc.roundedRect(15, 131, 180, 55, 3, 3, "FD");
        doc.setFont("helvetica", "normal"); doc.setFontSize(9.5); doc.setTextColor(textCol[0], textCol[1], textCol[2]);
        let cleanMenu = sim.specificMenu.replace(/<strong>/g, '').replace(/<\/strong>/g, '').replace(/<br>/g, '\n');
        doc.text(cleanMenu, 20, 141, { maxWidth: 170 });

        let vetNotes = document.getElementById('vetNotesInput').value.trim();
        if(vetNotes) {
            doc.setFont("helvetica", "bold"); doc.setFontSize(10); doc.setTextColor(accentCol[0], accentCol[1], accentCol[2]);
            doc.text("Notas Clínicas del Veterinario:", 15, 196);
            doc.setFont("helvetica", "normal"); doc.setFontSize(9); doc.setTextColor(textCol[0], textCol[1], textCol[2]);
            doc.text(vetNotes, 15, 203, { maxWidth: 180 });
        }

        doc.setFontSize(7.5); doc.setTextColor(mutedCol[0], mutedCol[1], mutedCol[2]);
        doc.text("Referencias APA: Case et al. (2011) | FEDIAF (2021) | NRC (2006) | Zoran (2002)", 105, 268, { align: "center" });
        doc.text("Simulador Terapéutico del Tío Nepomuceno - v12.", 105, 274, { align: "center" });

        return doc.output('blob');
    }

    function downloadSimulationPDF() {
        if(!currentSimulation.name) { alert('Realiza un cálculo primero.'); return; }
        const blob = generatePDFBlob(currentSimulation);
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = "Reporte_Tio_Nepomuceno_v12_" + currentSimulation.name + ".pdf";
        a.click();
    }
</script>

</body>
</html>
