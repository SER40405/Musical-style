<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>🎵 Онлайн Музыка с Плейлистами</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    body {
      background: linear-gradient(-45deg, #1f005c, #5b0060, #870160, #ac255e);
      background-size: 400% 400%;
      animation: gradient 15s ease infinite;
      color: white;
    }
    @keyframes gradient {
      0% {background-position: 0% 50%;}
      50% {background-position: 100% 50%;}
      100% {background-position: 0% 50%;}
    }
  </style>
</head>
<body class="font-sans">

  <header class="text-center py-6 bg-black/30 backdrop-blur-md shadow-lg rounded-b-3xl">
    <h1 class="text-4xl font-bold">🎵 Онлайн Музыка</h1>
    <p class="text-sm mt-2 text-gray-200">Загружай, слушай и сохраняй свои треки</p>
  </header>

  <section class="max-w-4xl mx-auto mt-6 text-center">
    <label class="bg-purple-600 hover:bg-purple-700 text-white px-6 py-3 rounded-full cursor-pointer inline-block">
      📁 Загрузить MP3
      <input type="file" accept=".mp3" id="fileInput" class="hidden" />
    </label>
    <button onclick="clearPlaylist()" class="ml-4 px-6 py-3 bg-red-600 hover:bg-red-700 rounded-full">
      ❌ Очистить плейлист
    </button>
  </section>

  <main id="musicContainer" class="p-6 grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6 max-w-6xl mx-auto mt-6">
    <!-- Треки появятся здесь -->
  </main>

  <footer class="text-center py-4 text-gray-200 mt-10">
    © 2025 Музыкальный сайт с плейлистами
  </footer>

  <script>
    const musicContainer = document.getElementById("musicContainer");

    function togglePlay(id, btn) {
      const audio = document.getElementById(id);
      if (audio.paused) {
        document.querySelectorAll("audio").forEach(el => {
          if (el !== audio) {
            el.pause();
            el.currentTime = 0;
            const b = el.nextElementSibling;
            if (b) b.textContent = "▶️ Играть";
          }
        });
        audio.play();
        btn.textContent = "⏸️ Пауза";
      } else {
        audio.pause();
        btn.textContent = "▶️ Играть";
      }
    }

    function addTrack(name, dataUrl, addToStorage = false) {
      const audioId = "user-audio-" + Date.now();
      const trackDiv = document.createElement("div");
      trackDiv.className = "bg-black/50 backdrop-blur-md p-4 rounded-2xl shadow-lg";

      trackDiv.innerHTML = 
        <img src="https://picsum.photos/seed/${Date.now()}/400/250" class="rounded-xl mb-3 w-full" alt="Обложка">
        <h2 class="text-xl font-semibold truncate">${name}</h2>
        <p class="text-sm text-gray-300">Мой трек</p>
        <audio id="${audioId}" src="${dataUrl}"></audio>
        <button onclick="togglePlay('${audioId}', this)" class="mt-3 px-4 py-2 bg-purple-600 rounded-full hover:bg-purple-700">
          ▶️ Играть
        </button>
      ;

      musicContainer.prepend(trackDiv);

      if (addToStorage) {
        const stored = JSON.parse(localStorage.getItem("playlist") || "[]");
        stored.push({ name: name, dataUrl: dataUrl });
        localStorage.setItem("playlist", JSON.stringify(stored));
      }
    }

    document.getElementById("fileInput").addEventListener("change", function (event) {
      const file = event.target.files[0];
      if (!file || !file.name.endsWith(".mp3")) return;

      const reader = new FileReader();
      reader.onload = function (e) {
        const audioUrl = e.target.result;
        addTrack(file.name, audioUrl, true);
      };
      reader.readAsDataURL(file);
    });

    function loadPlaylist() {
      const stored = JSON.parse(localStorage.getItem("playlist") || "[]");
      stored.forEach(track => addTrack(track.name, track.dataUrl, false));
    }

    function clearPlaylist() {
      localStorage.removeItem("playlist");
      musicContainer.innerHTML = "";
    }

    loadPlaylist();
  </script>

</body>
</html>
