<!DOCTYPE html>
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
  </style>
</head>
<body class="min-h-screen flex items-center justify-center p-6">
  <div id="app-container" class="w-full max-w-3xl bg-white rounded-xl shadow-2xl p-8 sm:p-10 lg:p-12">

    <!-- Initial -->
    <div id="initial-screen">
      <h1 class="text-5xl font-semibold text-center mb-4">Escape Room</h1>
      <p class="text-center text-gray-500 mb-8 text-lg">Wähle einen Modus</p>
      <div class="flex flex-col sm:flex-row gap-4">
        <button id="go-to-teacher-btn" class="btn-primary w-full">Lehrer‑Modus</button>
        <button id="go-to-student-btn" class="btn-secondary w-full">Schüler‑Modus</button>
      </div>
    </div>

    <!-- PIN Setup -->
    <div id="pin-setup-screen" class="hidden">
      <h2 class="text-3xl font-semibold mb-2">Lehrer‑PIN festlegen</h2>
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
      <h2 class="text-3xl font-semibold mb-2">Lehrer‑Login</h2>
      <p class="text-gray-500 mb-6 text-lg">Gib deine 4‑stellige PIN ein.</p>
      <form id="teacher-login-form" class="space-y-4">
        <input type="password" id="pin-input" maxlength="4"
               class="w-full text-center text-3xl tracking-widest p-4 border border-gray-300 rounded-lg bg-white shadow-sm"
               placeholder="••••" inputmode="numeric" required />
        <p id="login-error" class="hidden text-red-500 text-center">Falsche PIN!</p>
        <button type="submit" class="btn-primary w-full">Anmelden</button>
        <button type="button" id="back-to-main-from-login" class="btn-secondary w-full">Zurück</button>
      </form>
    </div>

    <!-- Teacher Dashboard -->
    <div id="teacher-dashboard" class="hidden">
      <div class="flex justify-between items-center mb-6">
        <h2 class="text-3xl font-semibold">Lehrer‑Dashboard</h2>
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
                   placeholder="Lösung hier eingeben" />
          </div>
          <button type="submit" class="btn-primary w-full">Rätsel speichern</button>
        </form>
      </div>
      <h3 class="text-2xl font-semibold mb-4">Bestehende Rätsel</h3>
      <div id="teacher-puzzle-list" class="space-y-4"></div>
    </div>

    <!-- Student View -->
    <div id="student-view" class="hidden">
      <div class="flex justify-between items-center mb-6">
        <h2 class="text-3xl font-semibold">Schüler‑Modus: Rätsel lösen</h2>
        <button id="exit-student-mode" class="btn-secondary">Zurück</button>
      </div>
      <div id="student-puzzle-container" class="space-y-6 mb-6"></div>
      <div class="mt-8 border-t pt-6">
        <p class="text-center text-lg mb-4">Fehlversuche: <span id="fail-counter" class="font-semibold text-red-600">0</span></p>
        <button id="open-escape-room-btn" class="btn-primary w-full text-xl py-4">Escape Room öffnen!</button>
        <p id="student-feedback" class="text-center mt-4 font-semibold"></p>
      </div>
      <div class="mt-12 text-center">
        <button id="reset-all-btn" class="btn-danger uppercase tracking-wide">Alles zurücksetzen</button>
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
    };

    const goToTeacherBtn = document.getElementById('go-to-teacher-btn');
    const goToStudentBtn = document.getElementById('go-to-student-btn');
    const pinSetupForm = document.getElementById('pin-setup-form');
    const teacherLoginForm = document.getElementById('teacher-login-form');
    const backToMainFromLoginBtn = document.getElementById('back-to-main-from-login');
    const exitTeacherModeBtn = document.getElementById('exit-teacher-mode');
    const exitStudentModeBtn = document.getElementById('exit-student-mode');

    let teacherPIN = null;

    function showScreen(screenName) {
      Object.values(screens).forEach(s => s.classList.add('hidden'));
      screens[screenName].classList.remove('hidden');
    }

    // Event Listeners
    goToTeacherBtn.addEventListener('click', () => {
      if (teacherPIN === null) {
        showScreen('pinSetup');
      } else {
        showScreen('teacherLogin');
      }
    });

    goToStudentBtn.addEventListener('click', () => {
      showScreen('studentView');
    });

    pinSetupForm.addEventListener('submit', e => {
      e.preventDefault();
      const pin = document.getElementById('new-pin-input').value.trim();
      if (/^\d{4}$/.test(pin)) {
        teacherPIN = pin;
        showScreen('teacherDashboard');
      } else {
        alert('Bitte eine gültige 4-stellige PIN eingeben.');
      }
    });

    teacherLoginForm.addEventListener('submit', e => {
      e.preventDefault();
      const pin = document.getElementById('pin-input').value.trim();
      const errorText = document.getElementById('login-error');
      if (pin === teacherPIN) {
        errorText.classList.add('hidden');
        showScreen('teacherDashboard');
      } else {
        errorText.classList.remove('hidden');
      }
    });

    backToMainFromLoginBtn.addEventListener('click', () => {
      showScreen('initial');
    });

    exitTeacherModeBtn.addEventListener('click', () => {
      showScreen('initial');
    });

    exitStudentModeBtn.addEventListener('click', () => {
      showScreen('initial');
    });
  </script>
</body>
</html>
