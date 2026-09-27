# Snack-booste
Une application qui boostera les données des clients 
contentDiv.innerHTMLI'll create a weather dashboard that fetches data from the Open-Meteo free weather API (no API key required):

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Weather Dashboard</title>
  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    }

    body {
      min-height: 100vh;
      background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
      padding: 20px;
    }

    .container {
      max-width: 1200px;
      margin: 0 auto;
    }

    .header {
      text-align: center;
      color: white;
      margin-bottom: 30px;
    }

    .header h1 {
      font-size: 2.5rem;
      margin-bottom: 10px;
    }

    .search-box {
      display: flex;
      gap: 10px;
      justify-content: center;
      margin-top: 20px;
      flex-wrap: wrap;
    }

    .search-box input {
      padding: 12px 16px;
      font-size: 1rem;
      border: none;
      border-radius: 8px;
      width: 300px;
      outline: none;
    }

    .search-box button {
      padding: 12px 24px;
      font-size: 1rem;
      background: #ff6b6b;
      color: white;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      transition: background 0.3s ease;
    }

    .search-box button:hover {
      background: #ff5252;
    }

    .weather-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
      gap: 20px;
      margin-bottom: 20px;
    }

    .weather-card {
      background: rgba(255, 255, 255, 0.95);
      border-radius: 16px;
      padding: 30px;
      box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
      backdrop-filter: blur(10px);
    }

    .weather-card.current {
      grid-column: span 1;
      background: linear-gradient(135deg, rgba(255, 255, 255, 0.98), rgba(240, 250, 255, 0.95));
    }

    .location-name {
      font-size: 1.8rem;
      font-weight: bold;
      color: #333;
      margin-bottom: 10px;
    }

    .coordinates {
      font-size: 0.9rem;
      color: #666;
      margin-bottom: 15px;
    }

    .temp-display {
      display: flex;
      align-items: center;
      gap: 20px;
      margin-bottom: 20px;
    }

    .temperature {
      font-size: 3.5rem;
      font-weight: bold;
      color: #667eea;
    }

    .weather-icon {
      font-size: 4rem;
      height: 80px;
      display: flex;
      align-items: center;
    }

    .description {
      font-size: 1.2rem;
      color: #555;
      text-transform: capitalize;
      margin-bottom: 20px;
    }

    .weather-details {
      display: grid;
      grid-template-columns: repeat(2, 1fr);
      gap: 15px;
      margin-top: 20px;
      padding-top: 20px;
      border-top: 1px solid #eee;
    }

    .detail {
      display: flex;
      flex-direction: column;
    }

    .detail-label {
      font-size: 0.9rem;
      color: #999;
      margin-bottom: 5px;
    }

    .detail-value {
      font-size: 1.2rem;
      font-weight: bold;
      color: #333;
    }

    .hourly-forecast {
      background: rgba(255, 255, 255, 0.95);
      border-radius: 16px;
      padding: 30px;
      box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
      margin-bottom: 20px;
    }

    .hourly-forecast h2 {
      margin-bottom: 20px;
      color: #333;
    }

    .hourly-cards {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
      gap: 15px;
    }

    .hourly-card {
      background: linear-gradient(135deg, #f5f7fa, #c3cfe2);
      border-radius: 12px;
      padding: 15px;
      text-align: center;
      transition: transform 0.3s ease;
    }

    .hourly-card:hover {
      transform: translateY(-5px);
    }

    .hourly-time {
      font-weight: bold;
      color: #333;
      margin-bottom: 8px;
    }

    .hourly-icon {
      font-size: 2rem;
      margin: 8px 0;
    }

    .hourly-temp {
      font-size: 1.1rem;
      color: #667eea;
      font-weight: bold;
    }

    .error {
      background: rgba(255, 107, 107, 0.9);
      color: white;
      padding: 20px;
      border-radius: 8px;
      text-align: center;
      font-size: 1.1rem;
    }

    .loading {
      text-align: center;
      color: white;
      font-size: 1.2rem;
      padding: 40px;
    }

    .spinner {
      border: 4px solid rgba(255, 255, 255, 0.3);
      border-top: 4px solid white;
      border-radius: 50%;
      width: 40px;
      height: 40px;
      animation: spin 1s linear infinite;
      margin: 20px auto;
    }

    @keyframes spin {
      0% { transform: rotate(0deg); }
      100% { transform: rotate(360deg); }
    }

    @media (max-width: 768px) {
      .header h1 {
        font-size: 1.8rem;
      }

      .search-box input {
        width: 100%;
      }

      .temperature {
        font-size: 2.5rem;
      }

      .hourly-cards {
        grid-template-columns: repeat(auto-fit, minmax(80px, 1fr));
      }
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="header">
      <h1>🌤️ Weather Dashboard</h1>
      <p>Search for any city to view current weather and hourly forecast</p>

      <div class="search-box">
        <input
          type="text"
          id="searchInput"
          placeholder="Enter city name..."
          autocomplete="off"
        />
        <button onclick="searchWeather()">Search</button>
        <button onclick="getLocationWeather()" style="background: #4ecdc4;">
          📍 Use My Location
        </button>
      </div>
    </div>

    <div id="content"></div>
  </div>

  <script>
    const contentDiv = document.getElementById('content');
    const searchInput = document.getElementById('searchInput');

    // Weather icon mapping
    const weatherIcons = {
      'clear sky': '☀️',
      'mainly clear': '🌤️',
      'partly cloudy': '⛅',
      'overcast': '☁️',
      'foggy': '🌫️',
      'drizzle': '🌦️',
      'rain': '🌧️',
      'snow': '❄️',
      'thunderstorm': '⛈️',
    };

    function getWeatherIcon(description) {
      const desc = description.toLowerCase();
      for (const [key, icon] of Object.entries(weatherIcons)) {
        if (desc.includes(key)) return icon;
      }
      return '🌤️';
    }

    async function fetchWeather(latitude, longitude) {
      try {
        contentDiv.innerHTML = '<div class="loading"><div class="spinner"></div>Loading weather data...</div>';

        const response = await fetch(
          `https://api.open-meteo.com/v1/forecast?latitude=${latitude}&longitude=${longitude}&current=temperature_2m,weather_code,wind_speed_10m,relative_humidity_2m,apparent_temperature&hourly=temperature_2m,weather_code&timezone=auto`
        );

        if (!response.ok) throw new Error('Failed to fetch weather data');

        const data = await response.json();
        const current = data.current;
        const hourly = data.hourly;

        // Get reverse geocoding for location name
        const geoResponse = await fetch(
          `https://nominatim.openstreetmap.org/reverse?format=json&lat=${latitude}&lon=${longitude}`
        );
        const geoData = await geoResponse.json();
        const locationName = geoData.address?.city || geoData.address?.town || geoData.address?.county || 'Unknown Location';

        // Get weather description
        const weatherDesc = getWeatherDescription(current.weather_code);

        // Build HTML
        let html = `
          <div class="weather-card current">
            <div class="location-name">${locationName}</div>
            <div class="coordinates">${latitude.toFixed(2)}°, ${longitude.toFixed(2)}°</div>

            <div class="temp-display">
              <div>
                <div class="temperature">${Math.round(current.temperature_2m)}°C</div>
                <div style="font-size: 0.9rem; color: #666; margin-top: 5px;">
                  Feels like ${Math.round(current.apparent_temperature)}°C
                </div>
              </div>
              <div class="weather-icon">${getWeatherIcon(weatherDesc)}</div>
            </div>

            <div class="description">${weatherDesc}</div>

            <div class="weather-details">
              <div class="detail">
                <div class="detail-label">💨 Wind Speed</div>
                <div class="detail-value">${current.wind_speed_10m.toFixed(1)} km/h</div>
              </div>
              <div class="detail">
                <div class="detail-label">💧 Humidity</div>
                <div class="detail-value">${current.relative_humidity_2m}%</div>
              </div>
            </div>
          </div>
        `;

        // Hourly forecast
        const now = new Date();
        const hourlyHtml = ['<div class="hourly-forecast"><h2>Hourly Forecast</h2><div class="hourly-cards">'];

        for (let i = 0; i < 24 && i < hourly.temperature_2m.length; i++) {
          const time = new Date(new Date(hourly.time[0]).getTime() + i * 60 * 60 * 1000);
          const hour = time.getHours().toString().padStart(2, '0') + ':00';
          const temp = Math.round(hourly.temperature_2m[i]);
          const desc = getWeatherDescription(hourly.weather_code[i]);

          hourlyHtml.push(`
            <div class="hourly-card">
              <div class="hourly-time">${hour}</div>
              <div class="hourly-icon">${getWeatherIcon(desc)}</div>
              <div class="hourly-temp">${temp}°C</div>
            </div>
          `);
        }

        hourlyHtml.push('</div></div>');
        html += hourlyHtml.join('');

        contentDiv.innerHTML = html;
      } catch (error) {
        contentDiv.innerHTML = `<div class="error">❌ ${error.message}</div>`;
      }
    }

    function getWeatherDescription(weatherCode) {
      const codes = {
        0: 'Clear Sky',
        1: 'Mainly Clear',
        2: 'Partly Cloudy',
        3: 'Overcast',
        45: 'Foggy',
        48: 'Foggy',
        51: 'Light Drizzle',
        53: 'Moderate Drizzle',
        55: 'Dense Drizzle',
        61: 'Slight Rain',
        63: 'Moderate Rain',
        65: 'Heavy Rain',
        71: 'Slight Snow',
        73: 'Moderate Snow',
        75: 'Heavy Snow',
        80: 'Slight Rain Showers',
        81: 'Moderate Rain Showers',
        82: 'Violent Rain Showers',
        85: 'Slight Snow Showers',
        86: 'Heavy Snow Showers',
        95: 'Thunderstorm',
        96: 'Thunderstorm with Hail',
        99: 'Thunderstorm with Hail',
      };
      return codes[weatherCode] || 'Unknown';
    }

    async function searchWeather() {
      const city = searchInput.value.trim();
      if (!city) return;

      try {
        contentDiv.innerHTML = '<div class="loading"><div class="spinner"></div>Searching for city...</div>';

        const response = await fetch(
          `https://nominatim.openstreetmap.org/search?format=json&q=${encodeURIComponent(city)}&limit=1`
        );

        if (!response.ok) throw new Error('City not found');

        const data = await response.json();
        if (data.length === 0) throw new Error('City not found');

        const { lat, lon } = data[0];
        fetchWeather(lat, lon);
        searchInput.value = '';
      } catch (error) {
        contentDiv.innerHTML = `<div class="error">❌ ${error.message}</div>`;
      }
    }

    function getLocationWeather() {
      if (!navigator.geolocation) {
        contentDiv.innerHTML = '<div class="error">❌ Geolocation not supported by your browser</div>';
        return;
      }

      contentDiv.innerHTML = '<div class="loading"><div class="spinner"></div>Getting your location...</div>';

      navigator.geolocation.getCurrentPosition(
        (position) => {
          const { latitude, longitude } = position.coords;
          fetchWeather(latitude, longitude);
        },
        (error) => {
          contentDiv.innerHTML = `<div class="error">❌ Unable to access your location: ${error.message}</div>`;
        }
      );
    }

    // Search on Enter key
    searchInput.addEventListener('keypress', (e) => {
      if (e.key === 'Enter') searchWeather();
    });

    // Load default city on page load
    window.addEventListener('load', () => {
      fetchWeather(40.7128, -74.006); // New York
    });
  </script>
</body>
</html>
``
