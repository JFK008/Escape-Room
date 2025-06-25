<html lang="de">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Escape Room</title>
  <style>
    body { font-family: sans-serif; padding: 20px; max-width: 600px; margin: auto; background: #f5f5f5; }
    .puzzle { background: white; border: 1px solid #ccc; padding: 15px; margin-bottom: 20px; border-radius: 8px; }
    .hidden { display: none; }
    img { max-width: 100%; }
    .solved { border-color: green; }
  </style>
</head>
<body>
  <h1>Escape Room</h1>
  <div id="puzzles"></div>
  <div id="final" class="hidden">
    <h2>🎉 Glückwunsch!</h2>
    <p>Alle Rätsel sind gelöst. Der Escape Room ist geöffnet!</p>
  </div>
  <script>
    const puzzles = [
      { image: "puzzle1.jpg", solution: "1" },
      { image: "puzzle2.jpg", solution: "2" },
      { image: "puzzle3.jpg", solution: "3" },
      { image: "puzzle4.jpg", solution: "4" }
    ];
    const container = document.getElementById("puzzles");
    puzzles.forEach((puzzle, index) => {
      const div = document.createElement("div");
      div.className = "puzzle";
      div.innerHTML = `
        <img src="${puzzle.image}" alt="Rätsel ${index + 1}" />
        <input type="text" placeholder="Lösung eingeben" id="input${index}" />
        <p id="result${index}"></p>
      `;
      container.appendChild(div);
      document.getElementById(`input${index}`).addEventListener("input", () => {
        const input = document.getElementById(`input${index}`).value.trim().toLowerCase();
        const result = document.getElementById(`result${index}`);
        if (input === puzzle.solution.toLowerCase()) {
          div.classList.add("solved");
          result.textContent = "✔️ Richtig!";
          checkAllSolved();
        } else {
          div.classList.remove("solved");
          result.textContent = "";
        }
      });
    });
    function checkAllSolved() {
      const solved = document.querySelectorAll(".puzzle.solved").length;
      if (solved === puzzles.length) {
        document.getElementById("final").classList.remove("hidden");
      }
    }
  </script>
</body>
</html>
