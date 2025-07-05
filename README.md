<html lang="de">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Escape Room Ersteller</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link href="https://fonts.googleapis.com/css2?family=SF+Pro+Display:wght@400;500;600;700&display=swap" rel="stylesheet" />
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/photoswipe/4.1.3/photoswipe.min.css">
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/photoswipe/4.1.3/default-skin/default-skin.min.css">

  <style>
    body { font-family: 'SF Pro Display', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif; background-color: #f2f2f7; color: #1c1c1e; }
    .btn-primary { background-color: #000; color: #fff; font-weight: 500; border-radius: 1rem; padding: .75rem; transition: background-color .2s; }
    .btn-primary:hover { background-color: #1c1c1e; }
    .btn-secondary { background-color: #e5e5ea; color: #1c1c1e; font-weight: 500; border-radius: 1rem; padding: .75rem; transition: background-color .2s; }
    .btn-secondary:hover { background-color: #d1d1d6; }
    .btn-danger { background-color: #ff3b30; color: #fff; font-weight: 500; border-radius: .75rem; padding: .5rem 1rem; transition: background-color .2s; }
    .btn-danger:hover { background-color: #e03125; }
    .hidden { display: none; }
    .puzzle-thumb { max-width: 60px; max-height: 60px; object-fit: cover; border-radius: 0.5rem; margin-right: 0.75rem; border: 1px solid #ccc; }
    #student-view .puzzle-thumb { cursor: zoom-in; }
    .puzzle-item { display: flex; align-items: center; justify-content: space-between; background-color: #fafafa; padding: 0.5rem 1rem; border-radius: 0.75rem; box-shadow: 0 0 5px rgba(0,0,0,0.05); }
    .puzzle-info { display: flex; align-items: center; flex: 1; }
    .puzzle-answer { font-weight: 600; color: #333; }
    .overlay-disabled, input[disabled] { pointer-events: none; opacity: 0.5; background-color: #e5e5ea; color: #6b7280; cursor: not-allowed; }
    /* NEU: Stile für Bearbeitungsbuttons */
    .btn-edit { background-color: #007aff; color: white; border-radius: 50%; width: 2rem; height: 2rem; display: inline-flex; justify-content: center; align-items: center; margin-left: 0.5rem; }
    .btn-arrow { background-color: #e5e5ea; border-radius: 50%; width: 2rem; height: 2rem; display: inline-flex; justify-content: center; align-items: center; margin-left: 0.25rem; }
    .btn-arrow:disabled { opacity: 0.3; cursor: not-allowed; }
    #final-reward-display a { color: #007aff; text-decoration: underline; }
  </style>
</head>
<body class="min-h-screen flex items-center justify-center p-6">
  <div id="app-container" class="w-full max-w-3xl bg-white rounded-xl shadow-2xl p-8 sm:p-10 lg:p-12">

    <!-- Initial -->
    <div id="initial-screen">
      <h1 class="text-5xl font-semibold text-center mb-4">Escape Room</h1>
      <p class="text-center text-gray-500 mb-8 text-lg">Wähle einen Modus</p>
      <div class="flex flex-col sm:flex-row gap-4">
        <button id="go-to-teacher-btn" class="btn-primary w-full">Spielleiter‑Modus</button>
        <button id="go-to-student-btn" class="btn-secondary w-full">Escape‑Modus</button>
      </div>
    </div>

    <!-- PIN Setup -->
    <div id="pin-setup-screen" class="hidden">
      <h2 class="text-3xl font-semibold mb-2">Spielleiter‑PIN festlegen</h2>
      <p class="text-gray-500 mb-6 text-lg">Bitte lege eine 4‑stellige PIN fest. Gut merken!</p>
      <form id="pin-setup-form" class="space-y-4">
        <input type="password" id="new-pin-input" maxlength="4" class="w-full text-center text-3xl tracking-widest p-4 border border-gray-300 rounded-lg bg-white shadow-sm" placeholder="••••" inputmode="numeric" required />
        <button type="submit" class="btn-primary w-full">PIN festlegen und starten</button>
      </form>
    </div>

    <!-- Teacher Login -->
    <div id="teacher-login-screen" class="hidden">
      <h2 class="text-3xl font-semibold mb-2">Spielleiter‑Login</h2>
      <p class="text-gray-500 mb-6 text-lg">Gib deine 4‑stellige PIN ein.</p>
      <form id="teacher-login-form" class="space-y-4">
        <input type="password" id="pin-input" maxlength="4" class="w-full text-center text-3xl tracking-widest p-4 border border-gray-300 rounded-lg bg-white shadow-sm" placeholder="••••" inputmode="numeric" required />
        <p id="login-error" class="hidden text-red-500 text-center">Falsche PIN!</p>
        <button type="submit" class="btn-primary w-full">Anmelden</button>
        <button type="button" id="back-to-main-from-login" class="btn-secondary w-full">Zurück</button>
      </form>
      <div class="mt-6 text-center">
         <button id="reset-pin-btn" class="text-sm text-gray-500 hover:text-red-600 hover:underline">PIN ZURÜCKSETZEN</button>
      </div>
    </div>

    <!-- Teacher Dashboard -->
    <div id="teacher-dashboard" class="hidden">
      <div class="flex justify-between items-center mb-6">
        <h2 class="text-3xl font-semibold">Spielleiter‑Dashboard</h2>
        <button id="exit-teacher-mode" class="btn-secondary">Modus verlassen</button>
      </div>

      <!-- NEU: Dashboard-Informationen -->
      <div class="grid grid-cols-1 md:grid-cols-2 gap-6 mb-6">
        <div class="bg-gray-50 p-4 rounded-xl shadow">
            <h3 class="text-lg font-semibold mb-2">Spielstand</h3>
            <p>Aktuelle Fehlversuche: <span id="dashboard-fail-count" class="font-bold">0</span></p>
        </div>
        <div class="bg-gray-50 p-4 rounded-xl shadow">
            <h3 class="text-lg font-semibold mb-1">Finale Belohnung</h3>
            <input type="text" id="final-reward-input" class="w-full p-2 border border-gray-300 rounded-lg bg-white" placeholder="Geheime Botschaft oder Link...">
        </div>
      </div>
      
      <div class="bg-gray-50 p-6 rounded-xl mb-6 shadow">
        <h3 id="puzzle-form-title" class="text-2xl font-semibold mb-4">Neues Rätsel erstellen</h3>
        <form id="add-puzzle-form" class="space-y-4">
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-1">Rätsel‑Foto</label>
            <input type="file" id="puzzle-image" accept="image/*" class="w-full text-sm text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-full file:border-0 file:text-sm file:font-medium file:bg-gray-200 file:text-gray-700 hover:file:bg-gray-300" />
            <img id="image-preview" src="" class="hidden mt-2" style="max-width: 100px; max-height: 100px; border-radius: 0.5rem;"/>
          </div>
          <div>
            <label for="puzzle-solution" class="block text-sm font-medium text-gray-700 mb-1">Lösungswort oder -zahl</label>
            <input type="text" id="puzzle-solution" required class="w-full p-3 border border-gray-300 rounded-lg bg-white shadow-sm" placeholder="Lösung hier eingeben" autocomplete="off" />
          </div>
          <!-- NEU: Hinweis-Feld -->
          <div>
            <label for="puzzle-hint" class="block text-sm font-medium text-gray-700 mb-1">Optionaler Hinweis</label>
            <input type="text" id="puzzle-hint" class="w-full p-3 border border-gray-300 rounded-lg bg-white shadow-sm" placeholder="Kleiner Tipp für die Spieler" autocomplete="off" />
          </div>
          <div class="flex gap-4">
            <button type="submit" id="save-puzzle-btn" class="btn-primary w-full">Rätsel speichern</button>
            <button type="button" id="cancel-edit-btn" class="btn-secondary w-full hidden">Abbrechen</button>
          </div>
        </form>
      </div>
      
      <div class="flex justify-between items-center mb-4">
        <h3 class="text-2xl font-semibold">Bestehende Rätsel</h3>
        <div class="flex gap-2 flex-wrap justify-end">
            <button id="reset-fails-btn" class="btn-secondary tracking-wide text-sm px-4 py-2">Fehlversuche zurücksetzen</button>
            <button id="teacher-reset-all-btn" class="btn-danger tracking-wide text-sm px-4 py-2">Alle Rätsel löschen</button>
        </div>
      </div>
      <div id="teacher-puzzle-list" class="space-y-4"></div>
    </div>

    <!-- Student View -->
    <div id="student-view" class="hidden">
      <div class="flex justify-between items-center mb-6">
        <h2 class="text-3xl font-semibold">Escape‑Modus: Rätsel lösen</h2>
        <button id="exit-student-mode" class="btn-secondary">Zurück</button>
      </div>
      <div id="student-puzzle-container" class="space-y-6 mb-6"></div>
      <div class="mt-8 border-t pt-6">
        <p class="text-center text-lg mb-4">Fehlversuche: <span id="fail-counter" class="font-semibold text-red-600">0</span></p>
        <button id="open-escape-room-btn" class="btn-primary w-full text-xl py-4">Escape Room öffnen!</button>
        <p id="student-feedback" class="text-center mt-4 font-semibold"></p>
      </div>
    </div>

    <!-- Success Screen -->
    <div id="success-screen" class="hidden">
      <h2 class="text-4xl font-bold text-center mb-6 text-green-700">Herzlichen Glückwunsch!</h2>
      <p class="text-center text-lg mb-4">Du hast alle Rätsel erfolgreich gelöst.</p>
      <!-- NEU: Container für die Belohnung -->
      <div id="final-reward-display" class="my-6 p-4 bg-yellow-100 border-l-4 border-yellow-500 text-yellow-700 text-center text-lg"></div>
      <p class="text-center text-lg mb-8">Fehlversuche: <span id="total-fails" class="font-semibold text-red-600">0</span></p>
      <div class="text-center">
        <button id="success-back-btn" class="btn-primary px-8 py-3">Zurück</button>
      </div>
    </div>
  </div>

  <div class="pswp" tabindex="-1" role="dialog" aria-hidden="true"><div class="pswp__bg"></div><div class="pswp__scroll-wrap"><div class="pswp__container"><div class="pswp__item"></div><div class="pswp__item"></div><div class="pswp__item"></div></div><div class="pswp__ui pswp__ui--hidden"><div class="pswp__top-bar"><div class="pswp__counter"></div><button class="pswp__button pswp__button--close" title="Schließen (Esc)"></button><button class="pswp__button pswp__button--zoom" title="Zoomen"></button><div class="pswp__preloader"><div class="pswp__preloader__icn"><div class="pswp__preloader__cut"><div class="pswp__preloader__donut"></div></div></div></div></div><div class="pswp__share-modal pswp__share-modal--hidden pswp__single-tap"><div class="pswp__share-tooltip"></div></div><button class="pswp__button pswp__button--arrow--left" title="Vorheriges (Pfeil links)"></button><button class="pswp__button pswp__button--arrow--right" title="Nächstes (Pfeil rechts)"></button><div class="pswp__caption"><div class="pswp__caption__center"></div></div></div></div></div>

  <script src="https://cdnjs.cloudflare.com/ajax/libs/photoswipe/4.1.3/photoswipe.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/photoswipe/4.1.3/photoswipe-ui-default.min.js"></script>

  <script>
    // Screens und UI-Elemente
    const screens = { initial: document.getElementById('initial-screen'), pinSetup: document.getElementById('pin-setup-screen'), teacherLogin: document.getElementById('teacher-login-screen'), teacherDashboard: document.getElementById('teacher-dashboard'), studentView: document.getElementById('student-view'), success: document.getElementById('success-screen'), };
    const goToTeacherBtn = document.getElementById('go-to-teacher-btn'), goToStudentBtn = document.getElementById('go-to-student-btn');
    const pinSetupForm = document.getElementById('pin-setup-form'), newPinInput = document.getElementById('new-pin-input');
    const teacherLoginForm = document.getElementById('teacher-login-form'), pinInput = document.getElementById('pin-input'), loginError = document.getElementById('login-error'), backToMainFromLogin = document.getElementById('back-to-main-from-login'), resetPinBtn = document.getElementById('reset-pin-btn');
    const addPuzzleForm = document.getElementById('add-puzzle-form'), puzzleImageInput = document.getElementById('puzzle-image'), puzzleSolutionInput = document.getElementById('puzzle-solution'), teacherPuzzleList = document.getElementById('teacher-puzzle-list'), teacherResetAllBtn = document.getElementById('teacher-reset-all-btn'), resetFailsBtn = document.getElementById('reset-fails-btn'), exitTeacherModeBtn = document.getElementById('exit-teacher-mode');
    const studentPuzzleContainer = document.getElementById('student-puzzle-container'), failCounterEl = document.getElementById('fail-counter'), openEscapeRoomBtn = document.getElementById('open-escape-room-btn'), studentFeedback = document.getElementById('student-feedback'), exitStudentModeBtn = document.getElementById('exit-student-mode');
    const successScreen = document.getElementById('success-screen'), totalFailsEl = document.getElementById('total-fails'), successBackBtn = document.getElementById('success-back-btn');

    // NEU: Zusätzliche UI-Elemente
    const dashboardFailCount = document.getElementById('dashboard-fail-count');
    const finalRewardInput = document.getElementById('final-reward-input');
    const finalRewardDisplay = document.getElementById('final-reward-display');
    const puzzleHintInput = document.getElementById('puzzle-hint');
    const puzzleFormTitle = document.getElementById('puzzle-form-title');
    const imagePreview = document.getElementById('image-preview');
    const savePuzzleBtn = document.getElementById('save-puzzle-btn');
    const cancelEditBtn = document.getElementById('cancel-edit-btn');

    // Variablen aus localStorage laden
    let teacherPIN = localStorage.getItem('teacherPIN') || null;
    let puzzles = JSON.parse(localStorage.getItem('puzzles') || '[]');
    let failCount = parseInt(localStorage.getItem('failCount') || '0');
    let finalReward = localStorage.getItem('finalReward') || '';
    let studentInputs = JSON.parse(localStorage.getItem('studentInputs') || '{}');
    let solvedPuzzles = new Set();
    let editingPuzzleIndex = null; // Für Bearbeitungsmodus

    // NEU: Sound-Effekte
    const successSound = new Audio('data:audio/mpeg;base64,UklGRl9vT19XQVZFZm10IBAAAAABAAEAQB8AAEAfAAABAAgAZGF0YU0AAAAAAAAAAD//fwCA/wIA/wMA/wQA/wUA/wYA/wcA/wgA/wkA/woA/wsA/wwA/w0A/w4A/w8A/xAA/xEA/xIA/xMA/xQA/xUA/xYA/xcA/xkA/xoA/xsA/xwA/x0A/x4A/x8A/yAA/yIA/yMA/yQA/yUA/yYA/ycA/ygA/yka/zIA/zQA/zYA/zcA/zYA/zQA/zIA/zAA/y8A/y4A/y0A/yoA/yUA/yIA/yAA/x8A/x0A/xsA/xkA/xYA/xQA/xEA/w4A/wsA/wgA/wMA/v8A+v4A9f0A8P0A6v0A5/0A4/0A3P0A1/0A0v0AzP0Ay/0Axv0A');
    const errorSound = new Audio('data:audio/mpeg;base64,UklGRkYAAABXQVZFZm10IBAAAAABAAEAQB8AAEAfAAABAAgAZGF0YQIAAAD//v/9/vr+7f7q/ub+5v7m/uX+4/7i/uP+5P7l/uX+6P7s/vH/AP8F/xH/If8y/0X/Uv9m/3H/gv+L/5P/mv+f/6f/r/+z/7r/wv/G/8z/0//X/9v/3v/h/+T/6v/w//oA');

    // HILFSFUNKTIONEN ZUM SPEICHERN
    const savePuzzles = () => localStorage.setItem('puzzles', JSON.stringify(puzzles));
    const saveTeacherPIN = (pin) => { localStorage.setItem('teacherPIN', pin); teacherPIN = pin; };
    const saveFailCount = () => { localStorage.setItem('failCount', failCount); dashboardFailCount.textContent = failCount; };
    const saveFinalReward = () => { finalReward = finalRewardInput.value; localStorage.setItem('finalReward', finalReward); };
    const saveStudentInputs = () => localStorage.setItem('studentInputs', JSON.stringify(studentInputs));

    // UI-FUNKTIONEN
    const switchScreen = (toScreen) => { Object.values(screens).forEach(s => s.classList.add('hidden')); toScreen.classList.remove('hidden'); };

    const resetPuzzleForm = () => {
        addPuzzleForm.reset();
        imagePreview.src = '';
        imagePreview.classList.add('hidden');
        editingPuzzleIndex = null;
        puzzleFormTitle.textContent = 'Neues Rätsel erstellen';
        savePuzzleBtn.textContent = 'Rätsel speichern';
        cancelEditBtn.classList.add('hidden');
        puzzleImageInput.required = true;
    };

    // RENDER-FUNKTIONEN
    function renderTeacherPuzzles() {
      teacherPuzzleList.innerHTML = puzzles.length === 0 ? '<p class="text-gray-500 italic">Keine Rätsel vorhanden.</p>' : '';
      puzzles.forEach((puzzle, idx) => {
        const div = document.createElement('div');
        div.className = 'puzzle-item';
        const infoDiv = document.createElement('div');
        infoDiv.className = 'puzzle-info';
        const img = document.createElement('img');
        img.src = puzzle.image;
        img.alt = 'Vorschau';
        img.className = 'puzzle-thumb';
        infoDiv.appendChild(img);
        const answerSpan = document.createElement('span');
        answerSpan.className = 'puzzle-answer';
        answerSpan.textContent = puzzle.answer;
        infoDiv.appendChild(answerSpan);
        div.appendChild(infoDiv);

        const controlsDiv = document.createElement('div');
        controlsDiv.className = 'flex items-center';

        const upBtn = document.createElement('button');
        upBtn.innerHTML = '▲';
        upBtn.className = 'btn-arrow';
        upBtn.disabled = idx === 0;
        upBtn.onclick = () => { [puzzles[idx-1], puzzles[idx]] = [puzzles[idx], puzzles[idx-1]]; savePuzzles(); renderTeacherPuzzles(); };
        controlsDiv.appendChild(upBtn);
        
        const downBtn = document.createElement('button');
        downBtn.innerHTML = '▼';
        downBtn.className = 'btn-arrow';
        downBtn.disabled = idx === puzzles.length - 1;
        downBtn.onclick = () => { [puzzles[idx+1], puzzles[idx]] = [puzzles[idx], puzzles[idx+1]]; savePuzzles(); renderTeacherPuzzles(); };
        controlsDiv.appendChild(downBtn);

        const editBtn = document.createElement('button');
        editBtn.innerHTML = '✎';
        editBtn.className = 'btn-edit';
        editBtn.onclick = () => {
            editingPuzzleIndex = idx;
            puzzleFormTitle.textContent = `Rätsel ${idx + 1} bearbeiten`;
            puzzleImageInput.required = false;
            imagePreview.src = puzzle.image;
            imagePreview.classList.remove('hidden');
            puzzleSolutionInput.value = puzzle.answer;
            puzzleHintInput.value = puzzle.hint || '';
            savePuzzleBtn.textContent = 'Änderungen speichern';
            cancelEditBtn.classList.remove('hidden');
            window.scrollTo(0,0);
        };
        controlsDiv.appendChild(editBtn);

        const delBtn = document.createElement('button');
        delBtn.textContent = 'Löschen';
        delBtn.className = 'btn-danger ml-2 text-sm px-3 py-1';
        delBtn.onclick = () => { if (confirm('Rätsel wirklich löschen?')) { puzzles.splice(idx, 1); savePuzzles(); renderTeacherPuzzles(); }};
        controlsDiv.appendChild(delBtn);

        div.appendChild(controlsDiv);
        teacherPuzzleList.appendChild(div);
      });
    }

    function renderStudentPuzzles() {
      studentPuzzleContainer.innerHTML = puzzles.length === 0 ? '<p class="text-gray-500 italic text-center">Keine Rätsel vorhanden.</p>' : '';
      puzzles.forEach((puzzle, idx) => {
        const div = document.createElement('div');
        div.className = 'puzzle-item flex-col sm:flex-row sm:items-center gap-2 sm:gap-0';
        const infoDiv = document.createElement('div');
        infoDiv.className = 'puzzle-info';
        const img = document.createElement('img');
        img.src = puzzle.image;
        img.className = 'puzzle-thumb';
        img.onclick = () => openPhotoSwipe(puzzle.image);
        infoDiv.appendChild(img);
        
        const answerDiv = document.createElement('div');
        answerDiv.className = 'flex-grow';
        const answerLabel = document.createElement('label');
        answerLabel.textContent = `Lösung Rätsel ${idx + 1}:`;
        answerLabel.className = 'block font-semibold text-gray-700 mb-1';
        const input = document.createElement('input');
        input.type = 'text';
        input.className = 'p-2 rounded border border-gray-300 w-full';
        input.autocomplete = 'off';
        input.dataset.index = idx;
        input.value = studentInputs[idx] || ''; // NEU: Gespeicherte Eingabe wiederherstellen
        input.oninput = () => { studentInputs[idx] = input.value; saveStudentInputs(); }; // NEU: Eingabe speichern
        answerLabel.appendChild(input);
        answerDiv.appendChild(answerLabel);
        infoDiv.appendChild(answerDiv);

        if (puzzle.hint) {
            const hintBtn = document.createElement('button');
            hintBtn.textContent = 'Hinweis';
            hintBtn.className = 'btn-secondary text-sm px-3 py-1 ml-4';
            hintBtn.onclick = () => {
                if(confirm('Möchtest du einen Hinweis? Dies zählt als ein Fehlversuch.')){
                    alert(`Hinweis: ${puzzle.hint}`);
                    failCount++;
                    saveFailCount();
                    failCounterEl.textContent = failCount;
                    errorSound.play();
                }
            };
            infoDiv.appendChild(hintBtn);
        }
        div.appendChild(infoDiv);
        studentPuzzleContainer.appendChild(div);
      });
    }

    // EVENT LISTENERS
    goToTeacherBtn.addEventListener('click', () => {
      if (!teacherPIN) switchScreen(screens.pinSetup);
      else {
        dashboardFailCount.textContent = failCount;
        finalRewardInput.value = finalReward;
        switchScreen(screens.teacherLogin);
      }
      pinInput.value = '';
      loginError.classList.add('hidden');
    });

    goToStudentBtn.addEventListener('click', () => {
      solvedPuzzles = new Set();
      failCounterEl.textContent = failCount;
      studentFeedback.textContent = '';
      renderStudentPuzzles();
      switchScreen(screens.studentView);
    });

    pinSetupForm.addEventListener('submit', e => { e.preventDefault(); const pin = newPinInput.value.trim(); if (/^\d{4}$/.test(pin)) { saveTeacherPIN(pin); alert('PIN gesetzt. Bitte nun anmelden.'); switchScreen(screens.teacherLogin); pinInput.value = ''; } else alert('Bitte 4-stellige PIN eingeben.'); });

    teacherLoginForm.addEventListener('submit', e => {
      e.preventDefault();
      if (pinInput.value.trim() === teacherPIN) {
        loginError.classList.add('hidden');
        renderTeacherPuzzles();
        switchScreen(screens.teacherDashboard);
      } else {
        loginError.classList.remove('hidden');
        errorSound.play();
      }
    });

    addPuzzleForm.addEventListener('submit', e => {
        e.preventDefault();
        const file = puzzleImageInput.files[0];
        const answer = puzzleSolutionInput.value.trim();
        const hint = puzzleHintInput.value.trim();

        const processPuzzle = (imageData) => {
            const newPuzzleData = { image: imageData, answer, hint };
            if (editingPuzzleIndex !== null) {
                puzzles[editingPuzzleIndex] = newPuzzleData;
            } else {
                puzzles.push(newPuzzleData);
            }
            savePuzzles();
            renderTeacherPuzzles();
            resetPuzzleForm();
            alert('Rätsel gespeichert.');
        };
        
        if (file) {
            const reader = new FileReader();
            reader.onload = (evt) => processPuzzle(evt.target.result);
            reader.readAsDataURL(file);
        } else if (editingPuzzleIndex !== null) {
            processPuzzle(puzzles[editingPuzzleIndex].image); // Altes Bild beibehalten
        } else {
            alert('Bitte ein Bild auswählen.');
        }
    });

    cancelEditBtn.addEventListener('click', resetPuzzleForm);
    finalRewardInput.addEventListener('input', saveFinalReward);
    
    teacherResetAllBtn.addEventListener('click', () => { if (puzzles.length > 0 && confirm('Alle Rätsel wirklich löschen?')) { puzzles = []; savePuzzles(); renderTeacherPuzzles(); } });
    
    resetFailsBtn.addEventListener('click', () => { if (confirm('Fehlversuche auf 0 zurücksetzen?')) { failCount = 0; saveFailCount(); alert('Fehlversuche zurückgesetzt.'); } });

    resetPinBtn.addEventListener('click', () => {
        if (confirm('Wirklich PIN, alle Rätsel, Fehlversuche und Eingaben löschen? Dies kann nicht rückgängig gemacht werden.')) {
            localStorage.clear();
            teacherPIN = null; puzzles = []; failCount = 0; finalReward = ''; studentInputs = {};
            alert('Alle Daten zurückgesetzt.');
            switchScreen(screens.initial);
        }
    });

    openEscapeRoomBtn.addEventListener('click', () => {
      const inputs = studentPuzzleContainer.querySelectorAll('input[type="text"]');
      let allAreCorrect = puzzles.length > 0;
      inputs.forEach(input => {
        if (input.value.trim().toLowerCase() !== puzzles[input.dataset.index].answer.trim().toLowerCase()) {
            allAreCorrect = false;
        }
      });

      if (allAreCorrect) {
        successSound.play();
        studentFeedback.textContent = 'Perfekt! Alle Antworten sind richtig.';
        studentFeedback.classList.remove('text-red-500');
        studentFeedback.classList.add('text-green-500');
        
        studentInputs = {}; // Gespeicherte Eingaben löschen
        saveStudentInputs();

        totalFailsEl.textContent = failCount;
        if (finalReward) {
            finalRewardDisplay.classList.remove('hidden');
            // Prüfen, ob es ein Link ist
            if (finalReward.startsWith('http://') || finalReward.startsWith('https://')) {
                finalRewardDisplay.innerHTML = `Belohnung: <a href="${finalReward}" target="_blank" rel="noopener noreferrer">${finalReward}</a>`;
            } else {
                finalRewardDisplay.textContent = finalReward;
            }
        } else {
            finalRewardDisplay.classList.add('hidden');
        }
        switchScreen(screens.success);
      } else {
        errorSound.play();
        failCount++;
        saveFailCount();
        failCounterEl.textContent = failCount;
        studentFeedback.textContent = 'Leider nicht korrekt. Mindestens eine Antwort ist falsch.';
        studentFeedback.classList.add('text-red-500');
      }
    });

    // SONSTIGE LISTENERS & INITIALISIERUNG
    backToMainFromLogin.addEventListener('click', () => switchScreen(screens.initial));
    exitTeacherModeBtn.addEventListener('click', () => switchScreen(screens.initial));
    exitStudentModeBtn.addEventListener('click', () => switchScreen(screens.initial));
    successBackBtn.addEventListener('click', () => { renderStudentPuzzles(); switchScreen(screens.studentView); });
    
    function openPhotoSwipe(imageSrc) {
        const tempImage = new Image();
        tempImage.onload = function() {
            const items = [{ src: imageSrc, w: this.naturalWidth, h: this.naturalHeight }];
            const options = { index: 0, closeOnScroll: false, pinchToClose: true, fullscreenEl: false, zoomEl: true, shareEl: false, counterEl: false, arrowEl: false, preloaderEl: true, };
            new PhotoSwipe(document.querySelector('.pswp'), PhotoSwipeUI_Default, items, options).init();
        };
        tempImage.src = imageSrc;
    }

    switchScreen(screens.initial);
  </script>
</body>
</html>
