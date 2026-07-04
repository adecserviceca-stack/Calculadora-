# Calculadora-```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Calculadora Interactiva Expressive</title>
  <!-- Tailwind CSS para el diseño visual -->
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700&family=Share+Tech+Mono&display=swap');
    
    body {
      font-family: 'Plus+Jakarta+Sans', sans-serif;
      transition: background-color 0.3s ease, color 0.3s ease;
    }

    .digital-font {
      font-family: 'Share+Tech+Mono', monospace;
    }

    /* Animación sutil para los clics de los botones */
    .btn-active {
      transform: scale(0.95);
      filter: brightness(0.9);
    }
    
    /* Scrollbar minimalista para el historial */
    .custom-scroll::-webkit-scrollbar {
      width: 4px;
    }
    .custom-scroll::-webkit-scrollbar-track {
      background: transparent;
    }
    .custom-scroll::-webkit-scrollbar-thumb {
      background: rgba(156, 163, 175, 0.5);
      border-radius: 2px;
    }
  </style>
</head>
<body class="bg-slate-900 text-slate-100 min-h-screen flex flex-col items-center justify-center p-4 overflow-x-hidden">

  <!-- Contenedor Principal de la Calculadora -->
  <div class="w-full max-w-md bg-slate-800/90 backdrop-blur-md rounded-3xl shadow-2xl border border-slate-700/50 p-6 flex flex-col justify-between relative transition-all duration-300 overflow-hidden" id="calcContainer">
    
    <!-- Decoración de Fondo Sutil -->
    <div class="absolute -top-24 -left-24 w-48 h-48 bg-emerald-500/10 rounded-full blur-3xl pointer-events-none"></div>
    <div class="absolute -bottom-24 -right-24 w-48 h-48 bg-blue-500/10 rounded-full blur-3xl pointer-events-none"></div>

    <!-- Encabezado con Controles -->
    <div class="flex justify-between items-center mb-4 z-10">
      <!-- Título de la App -->
      <div class="flex items-center space-x-2">
        <span class="w-3 h-3 rounded-full bg-emerald-500 animate-pulse"></span>
        <h1 class="text-xs font-semibold uppercase tracking-wider text-slate-400">Calculadora Neural</h1>
      </div>
      
      <!-- Controles: Sonido, Historial, Tema -->
      <div class="flex space-x-3">
        <!-- Alternar Sonido -->
        <button id="toggleSound" class="p-2 rounded-xl bg-slate-700/50 text-slate-300 hover:bg-slate-700 transition" title="Activar/Desactivar Sonido">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path id="soundIconPath" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15.536 8.464a5 5 0 010 7.072m2.828-9.9a9 9 0 010 12.728M5.586 15H4a1 1 0 01-1-1v-4a1 1 0 011-1h1.586l4.707-4.707C10.923 3.663 12 4.109 12 5v14c0 .891-1.077 1.337-1.707.707L5.586 15z" />
          </svg>
        </button>
        
        <!-- Ver Historial -->
        <button id="toggleHistory" class="p-2 rounded-xl bg-slate-700/50 text-slate-300 hover:bg-slate-700 transition" title="Ver Historial">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
          </svg>
        </button>
        
        <!-- Alternar Tema -->
        <button id="toggleTheme" class="p-2 rounded-xl bg-slate-700/50 text-slate-300 hover:bg-slate-700 transition" title="Cambiar Tema">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z" />
          </svg>
        </button>
      </div>
    </div>

    <!-- Pantalla de la Calculadora -->
    <div class="bg-slate-900/80 border border-slate-700/30 rounded-2xl p-5 mb-5 text-right flex flex-col justify-between min-h-[120px] relative overflow-hidden shadow-inner z-10">
      <!-- Indicador visual de modo científico -->
      <span id="sciModeIndicator" class="absolute top-2 left-3 text-[9px] font-bold tracking-widest text-emerald-500 uppercase opacity-0 transition-opacity">Modo Científico</span>
      
      <!-- Entrada o expresión previa -->
      <div id="expression" class="text-sm text-slate-400 font-medium tracking-wide truncate min-h-[20px] mb-1"></div>
      
      <!-- Resultado actual / Entrada principal -->
      <div id="display" class="text-4xl font-semibold text-white tracking-tight truncate max-w-full overflow-x-auto custom-scroll digital-font">0</div>
    </div>

    <!-- Panel de Historial Desplegable -->
    <div id="historyPanel" class="hidden absolute top-[72px] left-6 right-6 bottom-6 bg-slate-900/95 border border-slate-700/80 rounded-2xl p-4 z-20 flex flex-col justify-between transition-all duration-300 shadow-xl backdrop-blur-lg">
      <div>
        <div class="flex justify-between items-center mb-3">
          <h3 class="text-sm font-semibold uppercase tracking-wider text-slate-300">Historial de Operaciones</h3>
          <button id="clearHistory" class="text-xs text-rose-400 hover:text-rose-300 transition">Borrar todo</button>
        </div>
        <div id="historyList" class="space-y-3 overflow-y-auto max-h-[280px] pr-1 custom-scroll text-right text-xs">
          <!-- El historial se inyecta dinámicamente aquí -->
          <div class="text-slate-500 italic text-center py-8">No hay operaciones recientes</div>
        </div>
      </div>
      <button id="closeHistory" class="mt-4 w-full py-2 bg-slate-800 text-slate-300 rounded-xl hover:bg-slate-700 transition font-medium text-xs">Cerrar Historial</button>
    </div>

    <!-- Alerta Notificación Personalizada -->
    <div id="customAlert" class="hidden absolute top-4 left-4 right-4 bg-emerald-500 text-slate-950 px-4 py-2 rounded-xl text-center text-xs font-semibold shadow-lg transition-all duration-300 z-30 transform -translate-y-2 opacity-0">
      Resultado copiado al portapapeles
    </div>

    <!-- Sección de Teclas -->
    <div class="grid grid-cols-4 gap-3 z-10">
      
      <!-- Fila de Funciones Avanzadas / Científicas (Compactas) -->
      <div class="col-span-4 grid grid-cols-5 gap-2 mb-1 border-b border-slate-700/30 pb-3" id="scientificPanel">
        <button class="sci-btn text-xs font-semibold py-1.5 px-1 bg-slate-700/30 text-slate-300 rounded-lg hover:bg-slate-700/60 transition" data-val="sin">sin</button>
        <button class="sci-btn text-xs font-semibold py-1.5 px-1 bg-slate-700/30 text-slate-300 rounded-lg hover:bg-slate-700/60 transition" data-val="cos">cos</button>
        <button class="sci-btn text-xs font-semibold py-1.5 px-1 bg-slate-700/30 text-slate-300 rounded-lg hover:bg-slate-700/60 transition" data-val="tan">tan</button>
        <button class="sci-btn text-xs font-semibold py-1.5 px-1 bg-slate-700/30 text-slate-300 rounded-lg hover:bg-slate-700/60 transition" data-val="^">^</button>
        <button class="sci-btn text-xs font-semibold py-1.5 px-1 bg-slate-700/30 text-slate-300 rounded-lg hover:bg-slate-700/60 transition" data-val="sqrt">√</button>
        
        <button class="sci-btn text-xs font-semibold py-1.5 px-1 bg-slate-700/30 text-slate-300 rounded-lg hover:bg-slate-700/60 transition" data-val="log">log</button>
        <button class="sci-btn text-xs font-semibold py-1.5 px-1 bg-slate-700/30 text-slate-300 rounded-lg hover:bg-slate-700/60 transition" data-val="pi">π</button>
        <button class="sci-btn text-xs font-semibold py-1.5 px-1 bg-slate-700/30 text-slate-300 rounded-lg hover:bg-slate-700/60 transition" data-val="e">e</button>
        <button class="sci-btn text-xs font-semibold py-1.5 px-1 bg-slate-700/30 text-slate-300 rounded-lg hover:bg-slate-700/60 transition" data-val="(">(</button>
        <button class="sci-btn text-xs font-semibold py-1.5 px-1 bg-slate-700/30 text-slate-300 rounded-lg hover:bg-slate-700/60 transition" data-val=")">)</button>
      </div>

      <!-- Fila 1 -->
      <button class="calc-btn bg-rose-500/10 text-rose-400 hover:bg-rose-500/20 font-bold p-4 rounded-2xl transition text-lg" data-val="C">C</button>
      <button class="calc-btn bg-slate-700/40 text-slate-300 hover:bg-slate-700/60 font-medium p-4 rounded-2xl transition text-lg" data-val="()">( )</button>
      <button class="calc-btn bg-slate-700/40 text-slate-300 hover:bg-slate-700/60 font-medium p-4 rounded-2xl transition text-lg" data-val="%">%</button>
      <button class="calc-btn bg-emerald-500/10 text-emerald-400 hover:bg-emerald-500/20 font-bold p-4 rounded-2xl transition text-xl" data-val="/">÷</button>

      <!-- Fila 2 -->
      <button class="calc-btn bg-slate-700/20 text-slate-100 hover:bg-slate-700/40 font-semibold p-4 rounded-2xl transition text-xl" data-val="7">7</button>
      <button class="calc-btn bg-slate-700/20 text-slate-100 hover:bg-slate-700/40 font-semibold p-4 rounded-2xl transition text-xl" data-val="8">8</button>
      <button class="calc-btn bg-slate-700/20 text-slate-100 hover:bg-slate-700/40 font-semibold p-4 rounded-2xl transition text-xl" data-val="9">9</button>
      <button class="calc-btn bg-emerald-500/10 text-emerald-400 hover:bg-emerald-500/20 font-bold p-4 rounded-2xl transition text-xl" data-val="*">×</button>

      <!-- Fila 3 -->
      <button class="calc-btn bg-slate-700/20 text-slate-100 hover:bg-slate-700/40 font-semibold p-4 rounded-2xl transition text-xl" data-val="4">4</button>
      <button class="calc-btn bg-slate-700/20 text-slate-100 hover:bg-slate-700/40 font-semibold p-4 rounded-2xl transition text-xl" data-val="5">5</button>
      <button class="calc-btn bg-slate-700/20 text-slate-100 hover:bg-slate-700/40 font-semibold p-4 rounded-2xl transition text-xl" data-val="6">6</button>
      <button class="calc-btn bg-emerald-500/10 text-emerald-400 hover:bg-emerald-500/20 font-bold p-4 rounded-2xl transition text-xl" data-val="-">−</button>

      <!-- Fila 4 -->
      <button class="calc-btn bg-slate-700/20 text-slate-100 hover:bg-slate-700/40 font-semibold p-4 rounded-2xl transition text-xl" data-val="1">1</button>
      <button class="calc-btn bg-slate-700/20 text-slate-100 hover:bg-slate-700/40 font-semibold p-4 rounded-2xl transition text-xl" data-val="2">2</button>
      <button class="calc-btn bg-slate-700/20 text-slate-100 hover:bg-slate-700/40 font-semibold p-4 rounded-2xl transition text-xl" data-val="3">3</button>
      <button class="calc-btn bg-emerald-500/10 text-emerald-400 hover:bg-emerald-500/20 font-bold p-4 rounded-2xl transition text-xl" data-val="+">+</button>

      <!-- Fila 5 -->
      <button class="calc-btn bg-slate-700/20 text-slate-100 hover:bg-slate-700/40 font-semibold p-4 rounded-2xl transition text-xl" data-val="0">0</button>
      <button class="calc-btn bg-slate-700/20 text-slate-100 hover:bg-slate-700/40 font-semibold p-4 rounded-2xl transition text-xl" data-val=".">.</button>
      <button class="calc-btn bg-slate-700/40 text-slate-300 hover:bg-slate-700/60 font-semibold p-4 rounded-2xl transition flex items-center justify-center" data-val="backspace" title="Retroceso">
        <!-- Icono de Retroceso -->
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 14l2-2m0 0l2-2m-2 2l-2-2m2 2l2 2M3 12l6.414 6.414A2 2 0 0010.828 19H19a2 2 0 002-2V7a2 2 0 00-2-2h-8.172a2 2 0 00-1.414.586L3 12z" />
        </svg>
      </button>
      <button class="calc-btn bg-emerald-500 hover:bg-emerald-400 text-slate-950 font-extrabold p-4 rounded-2xl transition text-2xl shadow-lg shadow-emerald-500/20" data-val="=">=</button>
    </div>

    <!-- Pie de página con el botón para copiar el resultado -->
    <div class="mt-4 flex justify-between items-center text-xs text-slate-500 px-1 z-10">
      <span class="hover:text-slate-400 transition cursor-pointer" id="copyResultBtn">Tap resultado para copiar 📋</span>
      <span>V1.4.0</span>
    </div>

  </div>

  <!-- Script Lógica de Funcionamiento -->
  <script>
    // Variables de Estado
    let currentInput = "0";
    let pendingExpression = "";
    let isEvaluated = false;
    let history = [];
    let isSoundActive = true;
    let isDarkMode = true;

    // Inicialización del Audio Context para efectos hápiticos sintéticos
    let audioCtx = null;
    function playBeep(type = 'click') {
      if (!isSoundActive) return;
      try {
        if (!audioCtx) {
          audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        }
        if (audioCtx.state === 'suspended') {
          audioCtx.resume();
        }
        
        const osc = audioCtx.createOscillator();
        const gain = audioCtx.createGain();
        osc.connect(gain);
        gain.connect(audioCtx.destination);

        if (type === 'click') {
          osc.type = 'sine';
          osc.frequency.setValueAtTime(600, audioCtx.currentTime); // Tono agradable
          gain.gain.setValueAtTime(0.05, audioCtx.currentTime);
          gain.gain.exponentialRampToValueAtTime(0.0001, audioCtx.currentTime + 0.08);
          osc.start();
          osc.stop(audioCtx.currentTime + 0.08);
        } else if (type === 'success') {
          osc.type = 'triangle';
          osc.frequency.setValueAtTime(800, audioCtx.currentTime);
          osc.frequency.exponentialRampToValueAtTime(1200, audioCtx.currentTime + 0.15);
          gain.gain.setValueAtTime(0.07, audioCtx.currentTime);
          gain.gain.exponentialRampToValueAtTime(0.0001, audioCtx.currentTime + 0.18);
          osc.start();
          osc.stop(audioCtx.currentTime + 0.18);
        } else if (type === 'error') {
          osc.type = 'sawtooth';
          osc.frequency.setValueAtTime(150, audioCtx.currentTime);
          gain.gain.setValueAtTime(0.1, audioCtx.currentTime);
          gain.gain.exponentialRampToValueAtTime(0.0001, audioCtx.currentTime + 0.25);
          osc.start();
          osc.stop(audioCtx.currentTime + 0.25);
        }
      } catch (e) {
        console.warn("Audio Context falló:", e);
      }
    }

    // Elementos DOM
    const displayEl = document.getElementById("display");
    const exprEl = document.getElementById("expression");
    const sciModeIndicator = document.getElementById("sciModeIndicator");
    const calcContainer = document.getElementById("calcContainer");
    const historyPanel = document.getElementById("historyPanel");
    const historyList = document.getElementById("historyList");
    const alertEl = document.getElementById("customAlert");

    // Formatear display para lectura visual humana clara
    function updateDisplay() {
      // Reemplazo de símbolos para mostrar al usuario
      let userFriendlyExpr = pendingExpression
        .replace(/\*/g, ' × ')
        .replace(/\//g, ' ÷ ')
        .replace(/\^/g, ' ^ ')
        .replace(/Math\.PI/g, 'π')
        .replace(/Math\.E/g, 'e')
        .replace(/Math\.sqrt\(/g, '√( ')
        .replace(/Math\.sin\((.*?)\*Math\.PI\/180\)/g, 'sin($1°)')
        .replace(/Math\.cos\((.*?)\*Math\.PI\/180\)/g, 'cos($1°)')
        .replace(/Math\.tan\((.*?)\*Math\.PI\/180\)/g, 'tan($1°)')
        .replace(/Math\.log10\(/g, 'log(');

      exprEl.textContent = userFriendlyExpr;

      // Mostrar el input actual en proceso
      let userFriendlyInput = currentInput
        .replace(/\*/g, '×')
        .replace(/\//g, '÷');
      
      displayEl.textContent = userFriendlyInput;

      // Si hay elementos de trigonometría en el flujo, prender indicador científico
      if (pendingExpression.includes('Math.') || currentInput.includes('(')) {
        sciModeIndicator.classList.remove('opacity-0');
        sciModeIndicator.classList.add('opacity-100');
      } else {
        sciModeIndicator.classList.remove('opacity-100');
        sciModeIndicator.classList.add('opacity-0');
      }
    }

    // Lógica del Procesamiento Matemático
    function handleInput(val) {
      playBeep('click');

      if (isEvaluated && !['+', '-', '*', '/', '%', '^'].includes(val)) {
        // Si ya dio resultado y escribe un número, limpia la pantalla para empezar de nuevo
        currentInput = "";
        pendingExpression = "";
        isEvaluated = false;
      } else if (isEvaluated) {
        // Si es un operador, mantiene el resultado para continuar acumulando
        isEvaluated = false;
      }

      switch (val) {
        case 'C':
          currentInput = "0";
          pendingExpression = "";
          break;

        case 'backspace':
          if (currentInput.length > 1) {
            currentInput = currentInput.slice(0, -1);
          } else {
            currentInput = "0";
          }
          break;

        case '.':
          if (!currentInput.includes('.')) {
            currentInput += '.';
          }
          break;

        case '()':
          // Lógica inteligente de paréntesis automático alternante
          const openCount = (pendingExpression.match(/\(/g) || []).length;
          const closeCount = (pendingExpression.match(/\)/g) || []).length;
          
          if (openCount > closeCount && currentInput !== "0" && !isNaN(currentInput.slice(-1))) {
            currentInput += ")";
          } else {
            if (currentInput === "0") {
              currentInput = "(";
            } else {
              currentInput += "(";
            }
          }
          break;

        case '%':
          // Porcentaje básico
          if (currentInput !== "0") {
            currentInput = (parseFloat(currentInput) / 100).toString();
          }
          break;

        // Casos Operadores Básicos
        case '+':
        case '-':
        case '*':
        case '/':
          prepareOperator(val);
          break;

        // Casos Científicos Directos
        case '^':
          prepareOperator('**'); // Internamente es exponenciación en JS
          break;

        case 'sqrt':
          currentInput = "Math.sqrt(";
          break;

        case 'sin':
          currentInput = "Math.sin(";
          break;
        
        case 'cos':
          currentInput = "Math.cos(";
          break;

        case 'tan':
          currentInput = "Math.tan(";
          break;

        case 'log':
          currentInput = "Math.log10(";
          break;

        case 'pi':
          currentInput = "Math.PI";
          break;

        case 'e':
          currentInput = "Math.E";
          break;

        case '(':
          if (currentInput === "0") currentInput = "(";
          else currentInput += "(";
          break;

        case ')':
          if (currentInput === "0") currentInput = ")";
          else currentInput += ")";
          break;

        case '=':
          evaluateExpression();
          break;

        default:
          // Manejo de dígitos ordinarios
          if (currentInput === "0" || currentInput === "Error") {
            currentInput = val;
          } else {
            currentInput += val;
          }
          break;
      }

      updateDisplay();
    }

    function prepareOperator(op) {
      if (currentInput !== "0" && currentInput !== "Error") {
        pendingExpression += currentInput + op;
        currentInput = "0";
      } else if (pendingExpression !== "") {
        // Permite cambiar de operador en el último minuto
        pendingExpression = pendingExpression.slice(0, -1) + op;
      }
    }

    // Evaluación Matemática Segura
    function evaluateExpression() {
      let finalExpression = pendingExpression + currentInput;

      // Autocierre de paréntesis huérfanos antes de evaluar
      const openCount = (finalExpression.match(/\(/g) || []).length;
      const closeCount = (finalExpression.match(/\)/g) || []).length;
      if (openCount > closeCount) {
        finalExpression += ")".repeat(openCount - closeCount);
      }

      // Reestructuraciones matemáticas para evitar fallos de sintaxis en funciones de JS
      // Soporte trigonométrico en grados sexagesimales (más intuitivo para el usuario)
      let evaluableStr = finalExpression
        .replace(/Math\.sin\(([^)]+)\)/g, 'Math.sin(($1) * Math.PI / 180)')
        .replace(/Math\.cos\(([^)]+)\)/g, 'Math.cos(($1) * Math.PI / 180)')
        .replace(/Math\.tan\(([^)]+)\)/g, 'Math.tan(($1) * Math.PI / 180)');

      try {
        // Evaluación segura evitando `eval` general usando construcción de función aislada
        // Solamente permitimos caracteres numéricos, operadores matemáticos estándar y funciones del módulo Math de JS.
        const sanitizado = evaluableStr.replace(/[^0-69+\-*/().%*\s,Math\.sqrt|Math\.sin|Math\.cos|Math\.tan|Math\.log10|Math\.PI|Math\.E|e]/g, '');
        
        let calculated = new Function(`return (${sanitizado})`)();
        
        if (calculated === undefined || isNaN(calculated) || !isFinite(calculated)) {
          throw new Error("Operación Inválida");
        }

        // Redondear a un máximo de 10 decimales para prevenir anomalías de punto flotante de JS (ej: 0.1 + 0.2)
        calculated = Math.round(calculated * 1e10) / 1e10;

        // Registrar en Historial
        addHistoryRecord(finalExpression, calculated);
        
        currentInput = calculated.toString();
        pendingExpression = "";
        isEvaluated = true;
        playBeep('success');

      } catch (err) {
        currentInput = "Error";
        pendingExpression = "";
        isEvaluated = true;
        playBeep('error');
      }
    }

    // Funciones del Historial
    function addHistoryRecord(expr, result) {
      let friendlyExpr = expr
        .replace(/\*/g, '×')
        .replace(/\//g, '÷')
        .replace(/Math\.PI/g, 'π')
        .replace(/Math\.E/g, 'e')
        .replace(/Math\.sqrt\(/g, '√(')
        .replace(/Math\.sin\(/g, 'sin(')
        .replace(/Math\.cos\(/g, 'cos(')
        .replace(/Math\.tan\(/g, 'tan(')
        .replace(/Math\.log10\(/g, 'log(');

      history.unshift({ expr: friendlyExpr, result });
      if (history.length > 20) history.pop(); // Mantener un límite óptimo
      renderHistory();
    }

    function renderHistory() {
      if (history.length === 0) {
        historyList.innerHTML = `<div class="text-slate-500 italic text-center py-8">No hay operaciones recientes</div>`;
        return;
      }

      historyList.innerHTML = history.map((item, idx) => `
        <div class="border-b border-slate-700/40 pb-2 cursor-pointer hover:bg-slate-800/50 p-2 rounded-lg transition" onclick="selectHistory(${idx})">
          <div class="text-slate-400 font-medium truncate">${item.expr}</div>
          <div class="text-emerald-400 font-semibold text-sm digital-font">${item.result}</div>
        </div>
      `).join('');
    }

    window.selectHistory = function(idx) {
      const record = history[idx];
      currentInput = record.result.toString();
      pendingExpression = "";
      isEvaluated = false;
      historyPanel.classList.add('hidden');
      updateDisplay();
      playBeep('click');
    };

    // Funciones del portapapeles con fallback seguro
    function copyResultToClipboard() {
      const textToCopy = displayEl.textContent;
      if (textToCopy === "0" || textToCopy === "Error") return;

      const dummy = document.createElement("textarea");
      document.body.appendChild(dummy);
      dummy.value = textToCopy;
      dummy.select();
      document.execCommand("copy");
      document.body.removeChild(dummy);

      // Mostrar banner de confirmación
      alertEl.classList.remove('hidden');
      setTimeout(() => {
        alertEl.classList.remove('opacity-0', '-translate-y-2');
        alertEl.classList.add('opacity-100', 'translate-y-0');
      }, 50);

      setTimeout(() => {
        alertEl.classList.remove('opacity-100', 'translate-y-0');
        alertEl.classList.add('opacity-0', '-translate-y-2');
        setTimeout(() => alertEl.classList.add('hidden'), 300);
      }, 2000);

      playBeep('success');
    }

    // Configuración de Eventos de Botón
    document.querySelectorAll('.calc-btn').forEach(button => {
      button.addEventListener('pointerdown', (e) => {
        button.classList.add('btn-active');
        const val = button.getAttribute('data-val');
        handleInput(val);
      });

      button.addEventListener('pointerup', () => {
        button.classList.remove('btn-active');
      });

      button.addEventListener('pointerleave', () => {
        button.classList.remove('btn-active');
      });
    });

    document.querySelectorAll('.sci-btn').forEach(button => {
      button.addEventListener('pointerdown', (e) => {
        button.classList.add('btn-active');
        const val = button.getAttribute('data-val');
        handleInput(val);
      });

      button.addEventListener('pointerup', () => {
        button.classList.remove('btn-active');
      });

      button.addEventListener('pointerleave', () => {
        button.classList.remove('btn-active');
      });
    });

    // Control del Panel Historial
    document.getElementById('toggleHistory').addEventListener('click', () => {
      historyPanel.classList.toggle('hidden');
      playBeep('click');
    });

    document.getElementById('closeHistory').addEventListener('click', () => {
      historyPanel.classList.add('hidden');
      playBeep('click');
    });

    document.getElementById('clearHistory').addEventListener('click', () => {
      history = [];
      renderHistory();
      playBeep('error');
    });

    // Cambiar de modo Silencioso / Sonido
    const toggleSoundBtn = document.getElementById('toggleSound');
    const soundIconPath = document.getElementById('soundIconPath');
    toggleSoundBtn.addEventListener('click', () => {
      isSoundActive = !isSoundActive;
      if (isSoundActive) {
        toggleSoundBtn.classList.remove('text-slate-500');
        toggleSoundBtn.classList.add('text-slate-300');
        soundIconPath.setAttribute('d', 'M15.536 8.464a5 5 0 010 7.072m2.828-9.9a9 9 0 010 12.728M5.586 15H4a1 1 0 01-1-1v-4a1 1 0 011-1h1.586l4.707-4.707C10.923 3.663 12 4.109 12 5v14c0 .891-1.077 1.337-1.707.707L5.586 15z');
        playBeep('click');
      } else {
        toggleSoundBtn.classList.remove('text-slate-300');
        toggleSoundBtn.classList.add('text-slate-500');
        soundIconPath.setAttribute('d', 'M5.586 15H4a1 1 0 01-1-1v-4a1 1 0 011-1h1.586l4.707-4.707C10.923 3.663 12 4.109 12 5v14c0 .891-1.077 1.337-1.707.707L5.586 15zm11.364-8.486l-6.364 6.364m0-6.364l6.364 6.364'); // Icono mute con aspa simple
      }
    });

    // Copiar Resultado al dar clic en el panel
    document.getElementById('copyResultBtn').addEventListener('click', copyResultToClipboard);
    displayEl.addEventListener('click', copyResultToClipboard);

    // Cambiar Tema (Oscuro / Claro)
    document.getElementById('toggleTheme').addEventListener('click', () => {
      isDarkMode = !isDarkMode;
      playBeep('click');
      if (isDarkMode) {
        document.body.classList.remove('bg-slate-100', 'text-slate-800');
        document.body.classList.add('bg-slate-900', 'text-slate-100');
        calcContainer.classList.remove('bg-white', 'border-slate-200');
        calcContainer.classList.add('bg-slate-800/90', 'border-slate-700/50');
      } else {
        document.body.classList.remove('bg-slate-900', 'text-slate-100');
        document.body.classList.add('bg-slate-100', 'text-slate-800');
        calcContainer.classList.remove('bg-slate-800/90', 'border-slate-700/50');
        calcContainer.classList.add('bg-white', 'border-slate-200', 'shadow-lg');
      }
    });

    // Soporte para entrada directa desde el teclado de la computadora
    document.addEventListener('keydown', (e) => {
      const key = e.key;
      if (!isNaN(key)) {
        handleInput(key);
      } else if (['+', '-', '*', '/', '%', '(', ')', '.'].includes(key)) {
        handleInput(key);
      } else if (key === 'Enter' || key === '=') {
        e.preventDefault();
        handleInput('=');
      } else if (key === 'Backspace') {
        handleInput('backspace');
      } else if (key === 'Escape' || key === 'c' || key === 'C') {
        handleInput('C');
      }
    });

    // Render Inicial
    updateDisplay();
  </script>
</body>
</html>

```
