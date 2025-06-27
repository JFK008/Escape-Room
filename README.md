<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Escape Room Ersteller</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Inter', sans-serif; }
        .puzzle-card {
            display: flex;
            flex-direction: column;
            gap: 1rem;
            border: 1px solid #e5e7eb;
            border-radius: 0.5rem;
            padding: 1rem;
            background-color: white;
            box-shadow: 0 1px 3px 0 rgb(0 0 0 / 0.1), 0 1px 2px -1px rgb(0 0 0 / 0.1);
        }
        .puzzle-card img {
            max-height: 300px;
            width: 100%;
            object-fit: contain;
            border-radius: 0.25rem;
        }
        .hidden { display: none; }
    </style>
</head>
<body class="bg-gray-100 min-h-screen flex items-center justify-center p-4">

    <div id="app-container" class="w-full max-w-2xl mx-auto bg-white rounded-xl shadow-lg p-6 md:p-8">

        <!-- Initial Screen -->
        <div id="initial-screen">
            <h1 class="text-3xl font-bold text-center text-gray-800 mb-2">Escape Room</h1>
            <p class="text-center text-gray-500 mb-8">Wähle einen Modus</p>
            <div class="flex flex-col md:flex-row gap-4">
                <button id="go-to-teacher-btn" class="w-full bg-blue-600 text-white font-bold py-3 px-4 rounded-lg hover:bg-blue-700 transition-colors">
                    Lehrer-Modus
                </button>
                <button id="go-to-student-btn" class="w-full bg-green-600 text-white font-bold py-3 px-4 rounded-lg hover:bg-green-700 transition-colors">
                    Schüler-Modus
                </button>
            </div>
        </div>

        <!-- PIN Setup Screen -->
        <div id="pin-setup-screen" class="hidden">
            <h2 class="text-2xl font-bold text-gray-800 mb-4">Lehrer-PIN festlegen</h2>
            <p class="text-gray-600 mb-6">Bitte lege eine 4-stellige PIN fest, um den Lehrer-Bereich zu schützen. Merke dir diese PIN gut!</p>
            <form id="pin-setup-form" class="space-y-4">
                <input type="password" id="new-pin-input" inputmode="numeric" pattern="[0-9]{4}" title="Vierstelliger numerischer PIN" placeholder="4-stellige PIN eingeben" maxlength="4" class="w-full p-3 border border-gray-300 rounded-lg text-center text-2xl tracking-widest" required>
                <button type="submit" class="w-full bg-blue-600 text-white font-bold py-3 px-4 rounded-lg hover:bg-blue-700 transition-colors">PIN festlegen und starten</button>
            </form>
        </div>
        
        <!-- Teacher Login Screen -->
        <div id="teacher-login-screen" class="hidden">
            <h2 class="text-2xl font-bold text-gray-800 mb-4">Lehrer-Login</h2>
            <p class="text-gray-600 mb-6">Bitte gib deine 4-stellige PIN ein.</p>
            <form id="teacher-login-form" class="space-y-4">
                <input type="password" id="pin-input" inputmode="numeric" pattern="[0-9]{4}" placeholder="PIN eingeben" maxlength="4" class="w-full p-3 border border-gray-300 rounded-lg text-center text-2xl tracking-widest" required>
                <p id="login-error" class="text-red-500 text-center hidden">Falsche PIN!</p>
                <button type="submit" class="w-full bg-blue-600 text-white font-bold py-3 px-4 rounded-lg hover:bg-blue-700 transition-colors">Anmelden</button>
                <button type="button" id="back-to-main-from-login" class="w-full bg-gray-500 text-white font-bold py-2 px-4 rounded-lg hover:bg-gray-600 transition-colors">Zurück</button>
            </form>
        </div>

        <!-- Teacher Dashboard -->
        <div id="teacher-dashboard" class="hidden">
            <div class="flex justify-between items-center mb-6">
                <h2 class="text-2xl font-bold text-gray-800">Lehrer-Dashboard</h2>
                <button id="exit-teacher-mode" class="bg-gray-500 text-white font-bold py-2 px-4 rounded-lg hover:bg-gray-600 transition-colors">Modus verlassen</button>
            </div>
            
            <div class="bg-gray-50 p-6 rounded-lg mb-6">
                <h3 class="text-xl font-bold text-gray-700 mb-4">Neues Rätsel erstellen</h3>
                <form id="add-puzzle-form" class="space-y-4">
                    <div>
                        <label for="puzzle-image" class="block text-sm font-medium text-gray-700 mb-1">Rätsel-Foto</label>
                        <input type="file" id="puzzle-image" accept="image/*" class="w-full text-sm text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-full file:border-0 file:text-sm file:font-semibold file:bg-blue-50 file:text-blue-700 hover:file:bg-blue-100" required>
                    </div>
                    <div>
                        <label for="puzzle-solution" class="block text-sm font-medium text-gray-700 mb-1">Lösungswort oder -zahl</label>
                        <input type="text" id="puzzle-solution" placeholder="Lösung hier eingeben" class="w-full p-3 border border-gray-300 rounded-lg" required>
                    </div>
                    <button type="submit" class="w-full bg-blue-600 text-white font-bold py-3 px-4 rounded-lg hover:bg-blue-700 transition-colors">Rätsel speichern</button>
                </form>
            </div>

            <h3 class="text-xl font-bold text-gray-700 mb-4">Bestehende Rätsel</h3>
            <div id="teacher-puzzle-list" class="space-y-4">
                <!-- Puzzles will be dynamically inserted here -->
            </div>
        </div>

        <!-- Student View -->
        <div id="student-view" class="hidden">
            <div class="flex justify-between items-center mb-6">
                 <h2 class="text-2xl font-bold text-gray-800">Schüler-Modus: Löst die Rätsel!</h2>
                 <button id="exit-student-mode" class="bg-gray-500 text-white font-bold py-2 px-4 rounded-lg hover:bg-gray-600 transition-colors">Zurück</button>
            </div>
           
            <div id="student-puzzle-container" class="space-y-6 mb-6">
                <!-- Student puzzles will be dynamically inserted here -->
            </div>

            <div class="mt-8 border-t pt-6">
                <p class="text-center text-lg mb-4">Fehlversuche: <span id="fail-counter" class="font-bold text-red-600">0</span></p>
                <button id="open-escape-room-btn" class="w-full bg-green-600 text-white font-bold py-4 px-4 rounded-lg text-xl hover:bg-green-700 transition-colors">Escape Room öffnen!</button>
                <p id="student-feedback" class="text-center mt-4 font-semibold"></p>
            </div>

            <div class="mt-12 text-center">
                 <button id="reset-all-btn" class="text-red-500 hover:text-red-700 hover:underline">Alles zurücksetzen</button>
            </div>
        </div>
        
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            // State management object
            let state = {};

            // DOM Elements
            const screens = {
                initial: document.getElementById('initial-screen'),
                pinSetup: document.getElementById('pin-setup-screen'),
                teacherLogin: document.getElementById('teacher-login-screen'),
                teacherDashboard: document.getElementById('teacher-dashboard'),
                studentView: document.getElementById('student-view')
            };

            const pinSetupForm = document.getElementById('pin-setup-form');
            const newPinInput = document.getElementById('new-pin-input');

            const goToTeacherBtn = document.getElementById('go-to-teacher-btn');
            const goToStudentBtn = document.getElementById('go-to-student-btn');

            const teacherLoginForm = document.getElementById('teacher-login-form');
            const pinInput = document.getElementById('pin-input');
            const loginError = document.getElementById('login-error');
            const backToMainFromLogin = document.getElementById('back-to-main-from-login');

            const exitTeacherModeBtn = document.getElementById('exit-teacher-mode');
            const addPuzzleForm = document.getElementById('add-puzzle-form');
            const puzzleImageInput = document.getElementById('puzzle-image');
            const puzzleSolutionInput = document.getElementById('puzzle-solution');
            const teacherPuzzleList = document.getElementById('teacher-puzzle-list');
            
            const exitStudentModeBtn = document.getElementById('exit-student-mode');
            const studentPuzzleContainer = document.getElementById('student-puzzle-container');
            const openEscapeRoomBtn = document.getElementById('open-escape-room-btn');
            const failCounterEl = document.getElementById('fail-counter');
            const studentFeedback = document.getElementById('student-feedback');
            const resetAllBtn = document.getElementById('reset-all-btn');

            // --- Core Functions ---

            function loadState() {
                const savedState = localStorage.getItem('escapeRoomState');
                if (savedState) {
                    state = JSON.parse(savedState);
                } else {
                    state = {
                        pin: null,
                        puzzles: [],
                        failures: 0
                    };
                }
            }

            function saveState() {
                localStorage.setItem('escapeRoomState', JSON.stringify(state));
            }

            function showScreen(screenName) {
                Object.values(screens).forEach(screen => screen.classList.add('hidden'));
                if (screens[screenName]) {
                    screens[screenName].classList.remove('hidden');
                }
            }
            
            // --- Rendering Functions ---

            function renderTeacherPuzzles() {
                teacherPuzzleList.innerHTML = '';
                if (state.puzzles.length === 0) {
                    teacherPuzzleList.innerHTML = '<p class="text-center text-gray-500">Noch keine Rätsel erstellt.</p>';
                    return;
                }
                state.puzzles.forEach((puzzle, index) => {
                    const puzzleEl = document.createElement('div');
                    puzzleEl.className = 'puzzle-card flex-row items-center justify-between';
                    puzzleEl.innerHTML = `
                        <div class="flex items-center gap-4">
                            <img src="${puzzle.image}" alt="Rätsel ${index + 1}" class="w-20 h-20 object-cover rounded-md">
                            <div>
                                <p class="font-bold text-gray-800">Rätsel ${index + 1}</p>
                                <p class="text-sm text-gray-600">Lösung: <span class="font-mono bg-gray-200 px-1 rounded">${puzzle.solution}</span></p>
                            </div>
                        </div>
                        <button data-id="${puzzle.id}" class="delete-puzzle-btn bg-red-500 text-white font-bold py-2 px-3 rounded-lg hover:bg-red-600 text-sm">Löschen</button>
                    `;
                    teacherPuzzleList.appendChild(puzzleEl);
                });
            }

            function renderStudentPuzzles() {
                studentPuzzleContainer.innerHTML = '';
                 if (state.puzzles.length === 0) {
                    studentPuzzleContainer.innerHTML = '<p class="text-center text-gray-500">Der Lehrer hat noch keine Rätsel erstellt.</p>';
                    openEscapeRoomBtn.disabled = true;
                    openEscapeRoomBtn.classList.add('opacity-50', 'cursor-not-allowed');
                    return;
                }
                
                openEscapeRoomBtn.disabled = false;
                openEscapeRoomBtn.classList.remove('opacity-50', 'cursor-not-allowed');

                state.puzzles.forEach((puzzle, index) => {
                    const puzzleEl = document.createElement('div');
                    puzzleEl.className = 'puzzle-card';
                    puzzleEl.innerHTML = `
                        <p class="text-xl font-bold text-center text-gray-800">Rätsel ${index + 1}</p>
                        <img src="${puzzle.image}" alt="Rätselbild ${index + 1}">
                        <input type="text" data-id="${puzzle.id}" class="student-solution-input w-full p-3 border border-gray-300 rounded-lg text-center" placeholder="Gib hier die Lösung ein">
                    `;
                    studentPuzzleContainer.appendChild(puzzleEl);
                });
            }


            // --- Event Listeners ---

            pinSetupForm.addEventListener('submit', (e) => {
                e.preventDefault();
                state.pin = newPinInput.value;
                saveState();
                showScreen('teacherDashboard');
                renderTeacherPuzzles();
            });

            goToTeacherBtn.addEventListener('click', () => {
                if (!state.pin) {
                    showScreen('pinSetup');
                } else {
                    showScreen('teacherLogin');
                    pinInput.value = '';
                    loginError.classList.add('hidden');
                }
            });

            goToStudentBtn.addEventListener('click', () => {
                showScreen('studentView');
                failCounterEl.textContent = state.failures;
                studentFeedback.textContent = '';
                studentFeedback.classList.remove('text-green-600', 'text-red-500');
                renderStudentPuzzles();
            });

            backToMainFromLogin.addEventListener('click', () => showScreen('initial'));
            exitTeacherModeBtn.addEventListener('click', () => showScreen('initial'));
            exitStudentModeBtn.addEventListener('click', () => showScreen('initial'));

            teacherLoginForm.addEventListener('submit', (e) => {
                e.preventDefault();
                if (pinInput.value === state.pin) {
                    showScreen('teacherDashboard');
                    renderTeacherPuzzles();
                } else {
                    loginError.classList.remove('hidden');
                    pinInput.value = '';
                }
            });

            addPuzzleForm.addEventListener('submit', (e) => {
                e.preventDefault();
                const imageFile = puzzleImageInput.files[0];
                const solution = puzzleSolutionInput.value;

                if (imageFile && solution) {
                    const reader = new FileReader();
                    reader.onload = function(event) {
                        const newPuzzle = {
                            id: Date.now(),
                            image: event.target.result, // Base64 string
                            solution: solution.trim()
                        };
                        state.puzzles.push(newPuzzle);
                        saveState();
                        renderTeacherPuzzles();
                        addPuzzleForm.reset();
                    };
                    reader.readAsDataURL(imageFile);
                }
            });

            teacherPuzzleList.addEventListener('click', (e) => {
                if (e.target.classList.contains('delete-puzzle-btn')) {
                    const puzzleId = Number(e.target.dataset.id);
                    state.puzzles = state.puzzles.filter(p => p.id !== puzzleId);
                    saveState();
                    renderTeacherPuzzles();
                }
            });

            openEscapeRoomBtn.addEventListener('click', () => {
                const solutionInputs = document.querySelectorAll('.student-solution-input');
                let allCorrect = true;

                solutionInputs.forEach(input => {
                    const puzzleId = Number(input.dataset.id);
                    const puzzle = state.puzzles.find(p => p.id === puzzleId);
                    // Compare case-insensitively and trim whitespace
                    if (input.value.trim().toLowerCase() !== puzzle.solution.toLowerCase()) {
                        allCorrect = false;
                    }
                });

                if (allCorrect) {
                    studentFeedback.textContent = 'Herzlichen Glückwunsch! Alle Rätsel gelöst! Der Escape Room ist offen!';
                    studentFeedback.className = 'text-center mt-4 font-bold text-2xl text-green-600';
                } else {
                    state.failures++;
                    saveState();
                    failCounterEl.textContent = state.failures;
                    studentFeedback.textContent = 'Leider falsch! Überprüft eure Antworten noch einmal.';
                    studentFeedback.className = 'text-center mt-4 font-semibold text-red-500';
                }
            });
            
            resetAllBtn.addEventListener('click', () => {
                // We use a simple confirm dialog here. For a better UX, a custom modal would be preferable.
                const userConfirmed = confirm("Bist du sicher, dass du ALLES zurücksetzen möchtest? Alle Rätsel, der PIN und die Fehlversuche werden unwiderruflich gelöscht.");
                if (userConfirmed) {
                    localStorage.removeItem('escapeRoomState');
                    location.reload();
                }
            });


            // --- Initialization ---
            function init() {
                loadState();
                showScreen('initial');
            }

            init();
        });
    </script>
</body>
</html>
