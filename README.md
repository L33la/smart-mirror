<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Smart Mirror – Offenburg & Maynooth</title>
  <style>
    html, body {
      margin: 0;
      height: 100%;
      background-color: black;
      color: white;
      font-family: 'Arial', sans-serif;
    }

    body {
      display: flex;
      flex-direction: column;
      justify-content: center;  /* Vertikal zentrieren */
      align-items: center;      /* Horizontal zentrieren */
      height: 100vh;
      padding: 20px;
      box-sizing: border-box;
      text-align: center;
    }

    #quote {
      font-size: 1.8em;
      font-style: italic;
      color: white;
      margin: 0 0 8px 0;
      max-width: 90%;
      user-select: none;
    }

    .container {
      display: flex;
      justify-content: center;
      gap: 80px; /* Abstand zwischen den Städten */
      max-width: 1200px;
      width: 100%;
    }

    .city {
      flex: 1;
      min-width: 200px;
    }

    .city h2 {
      font-size: 2em;
      margin-bottom: 10px;
    }

    .time {
      font-size: 4em;
      line-height: 1;
    }

    .date {
      font-size: 1.5em;
      margin-top: 10px;
    }

    .weather {
      font-size: 1.5em;
      margin-top: 20px;
    }

    .temperature {
      font-size: 1.5em;
    }
  </style>
</head>
<body>
  <div id="quote"></div>

  <div class="container">
    <div class="city" id="offenburg">
      <h2>Offenburg</h2>
      <div id="time-offenburg" class="time"></div>
      <div id="date-offenburg" class="date"></div>
      <div id="weather-offenburg" class="weather"></div>
      <div id="temp-offenburg" class="temperature"></div>
    </div>

    <div class="city" id="maynooth">
      <h2>Maynooth</h2>
      <div id="time-maynooth" class="time"></div>
      <div id="date-maynooth" class="date"></div>
      <div id="weather-maynooth" class="weather"></div>
      <div id="temp-maynooth" class="temperature"></div>
    </div>
  </div>

  <script>
    const apiKey = '6184da7482d2c3270344dba8a5e27b1b';

    const johannesQuotes = [
      "Ein Tag ohne Lachen ist ein verlorener Tag.",
      "Mach das Beste aus dem, was du hast.",
      "Manchmal sind es die kleinen Dinge, die den Unterschied machen.",
      "Glaube an dich selbst und alles ist möglich.",
      "Die beste Zeit für einen Neuanfang ist jetzt.",
      "Jeder Moment ist eine neue Chance.",
      "Mut steht am Anfang des Handelns, Glück am Ende.",
      "Es gibt keine Zufälle, nur Begegnungen.",
      "Nicht weil es schwer ist, wagen wir es nicht, sondern weil wir es nicht wagen, ist es schwer.",
      "Träume nicht dein Leben, lebe deinen Traum."
    ];

    function updateQuote() {
      const day = new Date().getDate();
      const index = day % johannesQuotes.length;
      document.getElementById('quote').innerText = johannesQuotes[index];
    }

    function updateTime() {
      const now = new Date();

      const offenburgTime = new Intl.DateTimeFormat('de-DE', {
        timeZone: 'Europe/Berlin',
        hour: '2-digit',
        minute: '2-digit',
        second: '2-digit'
      }).format(now);
      const offenburgDate = new Intl.DateTimeFormat('de-DE', {
        timeZone: 'Europe/Berlin',
        weekday: 'long',
        year: 'numeric',
        month: 'long',
        day: 'numeric'
      }).format(now);
      document.getElementById('time-offenburg').innerText = offenburgTime;
      document.getElementById('date-offenburg').innerText = offenburgDate;

      const maynoothTime = new Intl.DateTimeFormat('de-DE', {
        timeZone: 'Europe/Dublin',
        hour: '2-digit',
        minute: '2-digit',
        second: '2-digit'
      }).format(now);
      const maynoothDate = new Intl.DateTimeFormat('de-DE', {
        timeZone: 'Europe/Dublin',
        weekday: 'long',
        year: 'numeric',
        month: 'long',
        day: 'numeric'
      }).format(now);
      document.getElementById('time-maynooth').innerText = maynoothTime;
      document.getElementById('date-maynooth').innerText = maynoothDate;
    }

    async function fetchWeather(city, tempId, weatherId) {
      const url = `https://api.openweathermap.org/data/2.5/weather?q=${city}&units=metric&lang=de&appid=${apiKey}`;

      try {
        const response = await fetch(url);
        const data = await response.json();

        const temp = data.main.temp.toFixed(1);
        const weatherDesc = data.weather[0].description;

        document.getElementById(tempId).innerText = `${temp}\u00B0C`;
        document.getElementById(weatherId).innerText =
          weatherDesc.charAt(0).toUpperCase() + weatherDesc.slice(1);

        if (city.startsWith('Offenburg')) {
          window.offenburgWeatherData = data;
        }
      } catch (error) {
        console.error('Fehler beim Abrufen der Wetterdaten:', error);
        document.getElementById(weatherId).innerText = 'Wetterdaten nicht verfügbar';
        document.getElementById(tempId).innerText = '';
      }
    }

    function initialize() {
      updateQuote();
      updateTime();
      fetchWeather('Offenburg,de', 'temp-offenburg', 'weather-offenburg');
      fetchWeather('Maynooth,ie', 'temp-maynooth', 'weather-maynooth');

      setInterval(updateTime, 1000);
      setInterval(() => {
        fetchWeather('Offenburg,de', 'temp-offenburg', 'weather-offenburg');
        fetchWeather('Maynooth,ie', 'temp-maynooth', 'weather-maynooth');
      }, 600000);
      setInterval(updateQuote, 86400000);
    }

    window.onload = initialize;
  </script>
</body>
</html>
