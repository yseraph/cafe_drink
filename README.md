<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>오늘의 추천 음료</title>
  <style>
    body {
      font-family: 'Noto Sans KR', sans-serif;
      background-color: #f3f3f3;
      text-align: center;
      padding: 40px;
    }
    .card {
      background-color: #fff;
      border-radius: 16px;
      box-shadow: 0 4px 12px rgba(0,0,0,0.1);
      padding: 24px;
      max-width: 320px;
      margin: auto;
    }
    .card img {
      width: 100%;
      border-radius: 12px;
    }
    .drink-name {
      font-size: 1.5em;
      margin-top: 16px;
      font-weight: bold;
    }
    .weather-info {
      font-size: 0.95em;
      color: #555;
      margin-top: 8px;
    }
  </style>
</head>
<body>

  <h1>🌤️ 오늘의 추천 음료</h1>
  <div class="card">
    <img id="drink-img" src="img/loading.jpg" alt="추천 음료 이미지">
    <div class="drink-name" id="drink-name">불러오는 중...</div>
    <div class="weather-info" id="weather-info">날씨 확인 중...</div>
  </div>

  <script>
    // Step 1: API 호출 정보
    const apiKey = "YOUR_API_KEY"; // 여기에 발급받은 API 키 삽입
    const city = "Seoul";
    const weatherApiUrl = `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${apiKey}&units=metric`;

    // Step 2: 추천 음료 매핑 테이블
    const drinkMap = [
      { condition: w => w.weather === "Rain", name: "따뜻한 라떼", img: "latte.jpg" },
      { condition: w => w.weather === "Snow", name: "핫초코", img: "hotchoco.jpg" },
      { condition: w => w.temp > 28, name: "아이스 아메리카노", img: "iceamericano.jpg" },
      { condition: w => w.temp < 15, name: "따뜻한 아메리카노", img: "hotamericano.jpg" },
      { condition: () => true, name: "바닐라빈 라떼", img: "vanillabean.jpg" }
    ];

    // Step 3: 날씨 정보 가져오기
    async function fetchWeather() {
      const response = await fetch(weatherApiUrl);
      const data = await response.json();
      return {
        temp: data.main.temp,
        weather: data.weather[0].main // e.g., "Clear", "Rain", "Snow"
      };
    }

    // Step 4: 조건에 따라 추천 음료 반환
    function getRecommendation(weatherData) {
      return drinkMap.find(drink => drink.condition(weatherData));
    }

    // Step 5: 웹페이지 업데이트
    async function showRecommendation() {
      try {
        const weather = await fetchWeather();
        const recommendation = getRecommendation(weather);
        document.getElementById("drink-name").innerText = recommendation.name;
        document.getElementById("drink-img").src = "img/" + recommendation.img;
        document.getElementById("weather-info").innerText =
          `현재 기온: ${weather.temp.toFixed(1)}℃ / 날씨: ${weather.weather}`;
      } catch (error) {
        document.getElementById("drink-name").innerText = "추천 음료 불러오기 실패";
        document.getElementById("weather-info").innerText = "";
        document.getElementById("drink-img").src = "img/error.jpg";
      }
    }

    // 실행
    showRecommendation();
  </script>

</body>
</html>
