<!-- index.html -->
<!DOCTYPE html>
<html lang="fa">
<head>
  <meta charset="UTF-8">
  <title>🌦️ پیش‌بینی ۵ روز آینده‌ی هوا</title>
  <style>
    body {
      font-family: sans-serif;
      background: linear-gradient(135deg, #74ebd5, #ACB6E5);
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: flex-start;
      min-height: 100vh;
      padding-top: 40px;
      text-align: center;
      color: #333;
      margin: 0;
    }
    h1 {
      color: #fff;
      text-shadow: 0 2px 4px rgba(0,0,0,0.3);
      margin-bottom: 20px;
    }
    input {
      padding: 10px;
      border: none;
      border-radius: 8px;
      width: 230px;
      text-align: center;
      font-size: 16px;
    }
    button {
      margin-top: 10px;
      padding: 10px 20px;
      border: none;
      border-radius: 8px;
      background: #333;
      color: #fff;
      cursor: pointer;
      transition: 0.3s;
    }
    button:hover {
      background: #555;
    }
    #weather {
      margin-top: 25px;
      background: rgba(255,255,255,0.85);
      padding: 20px;
      border-radius: 15px;
      box-shadow: 0 4px 15px rgba(0,0,0,0.2);
      width: 320px;
    }
    #forecast {
      display: flex;
      justify-content: center;
      flex-wrap: wrap;
      gap: 10px;
      margin-top: 20px;
    }
    .day {
      background: rgba(255,255,255,0.9);
      border-radius: 10px;
      padding: 10px;
      width: 100px;
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
    }
    .day img {
      width: 50px;
      height: 50px;
    }
  </style>
</head>
<body>
  <h1>🌦️ پیش‌بینی ۵ روز آینده‌ی هوا</h1>
  <input id="city" type="text" placeholder="نام شهر را وارد کنید">
  <button onclick="getWeather()">جستجو</button>
  <div id="weather">در انتظار انتخاب شهر...</div>
  <div id="forecast"></div>

  <script>
    const apiKey = "YOUR_API_KEY"; // 🔑 کلید API خودت از OpenWeatherMap

    async function getWeather() {
      const city = document.getElementById("city").value.trim();
      if (!city) {
        alert("لطفاً نام شهر را وارد کنید!");
        return;
      }

      try {
        // آب‌وهوا فعلی
        const currentRes = await fetch(
          `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${apiKey}&units=metric&lang=fa`
        );
        const currentData = await currentRes.json();

        if (currentData.cod === "404") {
          document.getElementById("weather").innerHTML = "❌ شهر پیدا نشد.";
          document.getElementById("forecast").innerHTML = "";
          return;
        }

        const icon = `https://openweathermap.org/img/wn/${currentData.weather[0].icon}@2x.png`;
        document.getElementById("weather").innerHTML = `
          <h2>${currentData.name}, ${currentData.sys.country}</h2>
          <img src="${icon}" alt="weather icon">
          <p>🌡️ دما: ${currentData.main.temp} °C</p>
          <p>💧 رطوبت: ${currentData.main.humidity}%</p>
          <p>🌥️ وضعیت: ${currentData.weather[0].description}</p>
        `;

        // پیش‌بینی ۵ روز آینده
        const forecastRes = await fetch(
          `https://api.openweathermap.org/data/2.5/forecast?q=${city}&appid=${apiKey}&units=metric&lang=fa`
        );
        const forecastData = await forecastRes.json();

        const forecastContainer = document.getElementById("forecast");
        forecastContainer.innerHTML = "";

        // نمایش فقط ۵ پیش‌بینی روزانه (هر ۸ داده = ۱ روز)
        for (let i = 0; i < forecastData.list.length; i += 8) {
          const day = forecastData.list[i];
          const date = new Date(day.dt * 1000).toLocaleDateString("fa-IR", {
            weekday: "long",
            month: "short",
            day: "numeric"
          });
          const icon = `https://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`;
          const temp = Math.round(day.main.temp);

          const div = document.createElement("div");
          div.className = "day";
          div.innerHTML = `
            <h4>${date}</h4>
            <img src="${icon}" alt="">
            <p>${temp}°C</p>
          `;
          forecastContainer.appendChild(div);
        }
      } catch (error) {
        document.getElementById("weather").innerHTML = "⚠️ خطا در دریافت اطلاعات!";
        document.getElementById("forecast").innerHTML = "";
      }
    }
  </script>
</body>
</html>
