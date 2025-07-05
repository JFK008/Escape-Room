<html lang="de">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Escape Room Ersteller</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link href="https://fonts.googleapis.com/css2?family=SF+Pro+Display:wght@400;500;600;700&display=swap" rel="stylesheet" />
  <style>
    body {
      font-family: 'SF Pro Display', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
      background-color: #f2f2f7;
      color: #1c1c1e;
    }
    .btn-primary {
      background-color: #000;
      color: #fff;
      font-weight: 500;
      border-radius: 1rem;
      padding: .75rem;
      transition: background-color .2s;
    }
    .btn-primary:hover {
      background-color: #1c1c1e;
    }
    .btn-secondary {
      background-color: #e5e5ea;
      color: #1c1c1e;
      font-weight: 500;
      border-radius: 1rem;
      padding: .75rem;
      transition: background-color .2s;
    }
    .btn-secondary:hover {
      background-color: #d1d1d6;
    }
    .btn-danger {
      background-color: #ff3b30;
      color: #fff;
      font-weight: 500;
      border-radius: .75rem;
      padding: .5rem 1rem;
      transition: background-color .2s;
    }
    .btn-danger:hover {
      background-color: #e03125;
    }
    .hidden {
      display: none;
    }
    .puzzle-thumb {
      max-width: 60px;
      max-height: 60px;
      object-fit: cover;
      border-radius: 0.5rem;
      margin-right: 0.75rem;
      border: 1px solid #ccc;
    }
    .puzzle-item {
      display: flex;
      align-items: center;
      justify-content: space-between;
      background-color: #fafafa;
      padding: 0.5rem 1rem;
      border-radius: 0.75rem;
      box-shadow: 0 0 5px rgba(0,0,0,0.05);
    }
    .puzzle-info {
      display: flex;
      align-items: center;
      flex: 1;
    }
    .puzzle-answer {
      font-weight: 600;
      color: #333;
    }
    .overlay-disabled {
      pointer-events: none;
      opacity: 0.5;
    }
    input[disabled] {
      background-color: #e5e5ea;
      color: #6b7280;
      cursor: not-allowed;
    }
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
        <input type="password" id="new-pin-input" maxlength="4"
               class="w-full text-center text-3xl tracking-widest p-4 border border-gray-300 rounded-lg bg-white shadow-sm"
               placeholder="••••" inputmode="numeric" required />
        <button type="submit" class="btn-primary w-full">PIN festlegen und starten</button>
      </form>
    </div>

    <!-- Teacher Login -->
    <div id="teacher-login-screen" class="hidden">
      <h2 class="text-3xl font-semibold mb-2">Spielleiter‑Login</h2>
      <p class="text-gray-500 mb-6 text-lg">Gib deine 4‑stellige PIN ein.</p>
      <form id="teacher-login-form" class="space-y-4">
        <input type="password" id="pin-input" maxlength="4"
               class="w-full text-center text-3xl tracking-widest p-4 border border-gray-300 rounded-lg bg-white shadow-sm"
               placeholder="••••" inputmode="numeric" required />
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
      <div class="bg-gray-50 p-6 rounded-xl mb-6 shadow">
        <h3 class="text-2xl font-semibold mb-4">Neues Rätsel erstellen</h3>
        <form id="add-puzzle-form" class="space-y-4">
          <div>
            <label for="puzzle-image" class="block text-sm font-medium text-gray-700 mb-1">Rätsel‑Foto</label>
            <input type="file" id="puzzle-image" accept="image/*" required
                   class="w-full text-sm text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-full file:border-0 file:text-sm file:font-medium file:bg-gray-200 file:text-gray-700 hover:file:bg-gray-300" />
          </div>
          <div>
            <label for="puzzle-solution" class="block text-sm font-medium text-gray-700 mb-1">Lösungswort oder -zahl</label>
            <input type="text" id="puzzle-solution" required
                   class="w-full p-3 border border-gray-300 rounded-lg bg-white shadow-sm"
                   placeholder="Lösung hier eingeben" autocomplete="off" />
          </div>
          <button type="submit" class="btn-primary w-full">Rätsel speichern</button>
        </form>
      </div>
      <div class="flex justify-between items-center mb-4">
        <h3 class="text-2xl font-semibold">Bestehende Rätsel</h3>
        <div class="flex gap-2">
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
      <p class="text-center text-lg mb-8">Fehlversuche: <span id="total-fails" class="font-semibold text-red-600">0</span></p>
      <div class="text-center">
        <button id="success-back-btn" class="btn-primary px-8 py-3">Zurück</button>
      </div>
    </div>

  </div>

  <script>
    // Screens
    const screens = {
      initial: document.getElementById('initial-screen'),
      pinSetup: document.getElementById('pin-setup-screen'),
      teacherLogin: document.getElementById('teacher-login-screen'),
      teacherDashboard: document.getElementById('teacher-dashboard'),
      studentView: document.getElementById('student-view'),
      success: document.getElementById('success-screen'),
    };

    // Buttons & Forms
    const goToTeacherBtn = document.getElementById('go-to-teacher-btn');
    const goToStudentBtn = document.getElementById('go-to-student-btn');

    const pinSetupForm = document.getElementById('pin-setup-form');
    const newPinInput = document.getElementById('new-pin-input');

    const teacherLoginForm = document.getElementById('teacher-login-form');
    const pinInput = document.getElementById('pin-input');
    const loginError = document.getElementById('login-error');
    const backToMainFromLogin = document.getElementById('back-to-main-from-login');
    const resetPinBtn = document.getElementById('reset-pin-btn');

    const addPuzzleForm = document.getElementById('add-puzzle-form');
    const puzzleImageInput = document.getElementById('puzzle-image');
    const puzzleSolutionInput = document.getElementById('puzzle-solution');
    const teacherPuzzleList = document.getElementById('teacher-puzzle-list');
    const teacherResetAllBtn = document.getElementById('teacher-reset-all-btn');
    const resetFailsBtn = document.getElementById('reset-fails-btn');
    const exitTeacherModeBtn = document.getElementById('exit-teacher-mode');

    const studentView = document.getElementById('student-view');
    const studentPuzzleContainer = document.getElementById('student-puzzle-container');
    const failCounterEl = document.getElementById('fail-counter');
    const openEscapeRoomBtn = document.getElementById('open-escape-room-btn');
    const studentFeedback = document.getElementById('student-feedback');
    const exitStudentModeBtn = document.getElementById('exit-student-mode');

    const successScreen = document.getElementById('success-screen');
    const totalFailsEl = document.getElementById('total-fails');
    const successBackBtn = document.getElementById('success-back-btn');

    // Variablen
    let teacherPIN = localStorage.getItem('teacherPIN') || null;
    let puzzles = JSON.parse(localStorage.getItem('puzzles') || '[]');
    let failCount = parseInt(localStorage.getItem('failCount') || '0');
    let solvedPuzzles = new Set();

    // Hilfsfunktionen
    function switchScreen(toScreen) {
      for (const key in screens) {
        screens[key].classList.add('hidden');
      }
      toScreen.classList.remove('hidden');
    }

    function savePuzzles() {
      localStorage.setItem('puzzles', JSON.stringify(puzzles));
    }

    function saveTeacherPIN(pin) {
      localStorage.setItem('teacherPIN', pin);
      teacherPIN = pin;
    }
    
    function saveFailCount() {
        localStorage.setItem('failCount', failCount);
    }

    function renderTeacherPuzzles() {
      teacherPuzzleList.innerHTML = '';
      if (puzzles.length === 0) {
        teacherPuzzleList.innerHTML = '<p class="text-gray-500 italic">Keine Rätsel vorhanden.</p>';
        return;
      }
      puzzles.forEach((puzzle, idx) => {
        const div = document.createElement('div');
        div.className = 'puzzle-item';

        const infoDiv = document.createElement('div');
        infoDiv.className = 'puzzle-info';

        const img = document.createElement('img');
        img.src = puzzle.image;
        img.alt = 'Vorschau Rätselbild';
        img.className = 'puzzle-thumb';
        infoDiv.appendChild(img);

        const answerSpan = document.createElement('span');
        answerSpan.className = 'puzzle-answer';
        answerSpan.textContent = puzzle.answer;
        infoDiv.appendChild(answerSpan);

        div.appendChild(infoDiv);

        const delBtn = document.createElement('button');
        delBtn.className = 'btn-danger text-sm px-3 py-1';
        delBtn.textContent = 'Löschen';
        delBtn.title = 'Dieses Rätsel löschen';
        delBtn.addEventListener('click', () => {
          if (confirm('Rätsel wirklich löschen?')) {
            puzzles.splice(idx, 1);
            savePuzzles();
            renderTeacherPuzzles();
          }
        });
        div.appendChild(delBtn);

        teacherPuzzleList.appendChild(div);
      });
    }

    function renderStudentPuzzles() {
      studentPuzzleContainer.innerHTML = '';
      if (puzzles.length === 0) {
        studentPuzzleContainer.innerHTML = '<p class="text-gray-500 italic text-center">Keine Rätsel vorhanden.</p>';
        return;
      }
      puzzles.forEach((puzzle, idx) => {
        const div = document.createElement('div');
        div.className = 'puzzle-item flex-col sm:flex-row sm:justify-between sm:items-center gap-2 sm:gap-0';

        const infoDiv = document.createElement('div');
        infoDiv.className = 'puzzle-info';

        const img = document.createElement('img');
        img.src = puzzle.image;
        img.alt = 'Rätselbild';
        img.className = 'puzzle-thumb';
        infoDiv.appendChild(img);

        const answerLabel = document.createElement('label');
        answerLabel.textContent = `Lösung Rätsel ${idx + 1}: `;
        answerLabel.className = 'block font-semibold text-gray-700 mb-1';

        const input = document.createElement('input');
        input.type = 'text';
        input.className = 'p-2 rounded border border-gray-300 w-full sm:w-64';
        input.autocomplete = 'off';
        input.dataset.index = idx;
        if (solvedPuzzles.has(idx)) {
          input.value = puzzles[idx].answer;
          input.disabled = true;
          input.classList.add('overlay-disabled');
        }

        answerLabel.appendChild(input);
        infoDiv.appendChild(answerLabel);

        div.appendChild(infoDiv);

        studentPuzzleContainer.appendChild(div);
      });
    }

    // Initial Button-Events
    goToTeacherBtn.addEventListener('click', () => {
      if (!teacherPIN) {
        switchScreen(screens.pinSetup);
        newPinInput.value = '';
      } else {
        switchScreen(screens.teacherLogin);
        pinInput.value = '';
        loginError.classList.add('hidden');
      }
    });

    goToStudentBtn.addEventListener('click', () => {
      solvedPuzzles = new Set();
      failCounterEl.textContent = failCount;
      studentFeedback.textContent = '';
      renderStudentPuzzles();
      switchScreen(screens.studentView);
    });

    // PIN Setup Form
    pinSetupForm.addEventListener('submit', e => {
      e.preventDefault();
      const pin = newPinInput.value.trim();
      if (/^\d{4}$/.test(pin)) {
        saveTeacherPIN(pin);
        alert('PIN wurde gesetzt. Bitte melde dich nun an.');
        switchScreen(screens.teacherLogin);
        pinInput.value = '';
        loginError.classList.add('hidden');
      } else {
        alert('Bitte eine 4-stellige PIN eingeben.');
      }
    });

    // Teacher Login Form
    teacherLoginForm.addEventListener('submit', e => {
      e.preventDefault();
      const pin = pinInput.value.trim();
      if (pin === teacherPIN) {
        loginError.classList.add('hidden');
        renderTeacherPuzzles();
        puzzleImageInput.value = '';
        puzzleSolutionInput.value = '';
        switchScreen(screens.teacherDashboard);
      } else {
        loginError.classList.remove('hidden');
      }
    });

    backToMainFromLogin.addEventListener('click', () => {
      switchScreen(screens.initial);
    });

    // PIN ZURÜCKSETZEN
    resetPinBtn.addEventListener('click', () => {
      if (confirm('Möchten Sie wirklich die PIN und alle gespeicherten Rätsel unwiderruflich löschen? Auch die Fehlversuche werden zurückgesetzt.')) {
        localStorage.removeItem('teacherPIN');
        localStorage.removeItem('puzzles');
        localStorage.removeItem('failCount');
        teacherPIN = null;
        puzzles = [];
        failCount = 0; 
        alert('PIN und alle Daten wurden erfolgreich zurückgesetzt.');
        switchScreen(screens.initial);
      }
    });

    // Add Puzzle Form
    addPuzzleForm.addEventListener('submit', e => {
      e.preventDefault();
      const file = puzzleImageInput.files[0];
      const answer = puzzleSolutionInput.value.trim();

      if (!file) {
        alert('Bitte ein Bild auswählen.');
        return;
      }
      if (!answer) {
        alert('Bitte eine Lösung eingeben.');
        return;
      }

      const reader = new FileReader();
      reader.onload = function(evt) {
        const dataUrl = evt.target.result;
        puzzles.push({ image: dataUrl, answer: answer });
        savePuzzles();
        renderTeacherPuzzles();
        puzzleImageInput.value = '';
        puzzleSolutionInput.value = '';
        alert('Rätsel gespeichert.');
      };
      reader.onerror = function() {
        alert('Fehler beim Lesen der Bilddatei.');
      };
      reader.readAsDataURL(file);
    });

    // Reset all puzzles - Teacher
    teacherResetAllBtn.addEventListener('click', () => {
      if (puzzles.length > 0 && confirm('Alle Rätsel wirklich löschen?')) {
        puzzles = [];
        savePuzzles();
        renderTeacherPuzzles();
      } else if (puzzles.length === 0) {
        alert('Es sind keine Rätsel zum Löschen vorhanden.');
      }
    });
    
    // Fehlversuche zurücksetzen
    resetFailsBtn.addEventListener('click', () => {
        if (confirm('Sollen die Fehlversuche für die nächste Runde wirklich auf 0 zurückgesetzt werden?')) {
            failCount = 0;
            saveFailCount();
            alert('Die Fehlversuche wurden zurückgesetzt.');
        }
    });

    exitTeacherModeBtn.addEventListener('click', () => {
      switchScreen(screens.initial);
    });

    // Exit Student Mode
    exitStudentModeBtn.addEventListener('click', () => {
      switchScreen(screens.initial);
    });

    // Open Escape Room button (prüft alle Lösungen)
    openEscapeRoomBtn.addEventListener('click', () => {
      const inputs = studentPuzzleContainer.querySelectorAll('input[type="text"]');
      let allAreCorrect = true;

      if (puzzles.length === 0) return;

      inputs.forEach(input => {
        const idx = parseInt(input.dataset.index);
        const userAnswer = input.value.trim().toLowerCase();
        const correctAnswer = puzzles[idx].answer.trim().toLowerCase();

        if (userAnswer !== correctAnswer) {
          allAreCorrect = false;
        }
      });

      if (allAreCorrect) {
        studentFeedback.textContent = 'Perfekt! Alle Antworten sind richtig.';
        studentFeedback.classList.remove('text-red-500');
        studentFeedback.classList.add('text-green-500');

        puzzles.forEach((_, idx) => solvedPuzzles.add(idx));

        totalFailsEl.textContent = failCount;
        switchScreen(screens.success);
      } else {
        failCount++;
        saveFailCount();
        failCounterEl.textContent = failCount;
        studentFeedback.textContent = 'Leider nicht korrekt. Mindestens eine Antwort ist falsch. Versuche es erneut!';
        studentFeedback.classList.add('text-red-500');
        studentFeedback.classList.remove('text-green-500');
      }
    });

    // Zurück-Button im Erfolgsscreen
    successBackBtn.addEventListener('click', () => {
      renderStudentPuzzles();
      switchScreen(screens.studentView);
    });

    // Initial Startscreen zeigen
    switchScreen(screens.initial);
  </script>
</body>
</html>
