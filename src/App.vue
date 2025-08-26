<script setup>
import { ref, onMounted } from "vue"

const weatherData = ref([])

const weatherCodeMap = {
  0: "Clear sky",
  1: "Mainly clear",
  2: "Partly cloudy",
  3: "Overcast",

  45: "Fog",
  48: "Depositing rime fog",

  51: "Drizzle: Light",
  53: "Drizzle: Moderate",
  55: "Drizzle: Dense",
  56: "Freezing drizzle: Light",
  57: "Freezing drizzle: Dense",

  61: "Rain: Slight",
  63: "Rain: Moderate",
  65: "Rain: Heavy",
  66: "Freezing rain: Light",
  67: "Freezing rain: Heavy",

  71: "Snow fall: Slight",
  73: "Snow fall: Moderate",
  75: "Snow fall: Heavy",
  77: "Snow grains",

  80: "Rain showers: Slight",
  81: "Rain showers: Moderate",
  82: "Rain showers: Violent",

  85: "Snow showers: Slight",
  86: "Snow showers: Heavy",

  95: "Thunderstorm: Slight or moderate",
  96: "Thunderstorm with slight hail",
  99: "Thunderstorm with heavy hail"
}

const weatherIconMap = {
  0: "clear",
  1: "clear",
  2: "mostly-clear",
  3: "overcast",

  45: "fog",
  48: "fog",

  51: "drizzle-light",
  53: "drizzle-light",
  55: "drizzle-heavy",
  56: "drizzle-light",

  61: "rain",
  63: "rain",
  65: "rain-heavy",
  66: "rain",
  67: "rain-heavy",
  71: "snow",
  73: "snow",
  75: "snow-heavy",
  77: "snow",

  80: "rain",
  81: "rain",
  82: "rain-heavy", 

  85: "snow",             
  86: "snow-heavy",        

  95: "thunderstorm-rain",
  96: "thunderstorm-rain",
  99: "thunderstorm-rain"
}




  async function fetchWeather(){
    const res = await fetch("https://api.open-meteo.com/v1/forecast?latitude=14.6042&longitude=120.9822&daily=weather_code,temperature_2m_max,temperature_2m_min,precipitation_sum,precipitation_probability_max,wind_speed_10m_max")
    if(!res.ok){
      throw new Error('error fetching weather data')
    }

    const data = await res.json();


   weatherData.value = data.daily.time.map((time, index) => {
      return {
        time,
        weather_code: data.daily.weather_code[index],
        temperature_2m_max: data.daily.temperature_2m_max[index],
        temperature_2m_min: data.daily.temperature_2m_min[index],
        precipitation_sum: data.daily.precipitation_sum[index],
        precipitation_probability_max: data.daily.precipitation_probability_max[index],
        wind_speed_10m_max: data.daily.wind_speed_10m_max[index]
      }
    })


    
  }

  function getWeatherIcon(weatherCode) {
  const iconName = weatherIconMap[weatherCode] || 'clear'
  return new URL(`./assets/icons/day/${iconName}.svg`, import.meta.url).href
}

  onMounted(()=>{
    fetchWeather()
  })



</script>

<template>

  <div>
    <h1>Weather Forecast</h1>

    <div class="weather-card" v-for="dailyWeather, index in weatherData">
      
      <div class="weather-header">
      <div class="weather-header-content">
        <img class="weather-icon" :src="getWeatherIcon(dailyWeather.weather_code)"></img>
        <div class="weather-header-info">
          <div>

            {{ weatherCodeMap[dailyWeather.weather_code]}}
          </div>
          <div>

            {{ dailyWeather.temperature_2m_min }} °C - {{ dailyWeather.temperature_2m_max }} °C
          </div>

        </div>
      </div>
  
    

      </div>
      
      <div class="weather-body">
      <div>Date: {{ dailyWeather.time}}</div>
      <div>Precipitation: {{ dailyWeather.precipitation_sum }}mm</div>
      <div>Precipitation Chance: {{ dailyWeather.precipitation_probability_max }}%</div>
      <div>Wind Speed: {{ dailyWeather.wind_speed_10m_max }}km/h</div>
      </div>

    
      <br>
    </div>
  
  </div>
  
</template>


<style>
  body{
    font-family: Arial, sans-serif;
    margin: 10px;
    padding: 4px;
    box-sizing: border-box;
  }

  .weather-card{
    display: flex;
    flex-direction: column;
    box-shadow: 0 4px 8px rgba(0,0,0,0.1);
    padding: 10px;
    margin: 10px;
    width: auto;
  }

  .weather-header{
    display: flex;
    flex-direction: column;
    justify-content: space-between;
  }

  .weather-header-content{
    display: flex;
    flex-direction: row;
    align-items: center;
    gap: 10px;
    font-size: 24px;
  }

  .weather-icon{
    width: 100px;
    height: 100px;
    background-color: rgb(255, 255, 255);
  }

  .weather-body{
    display: flex;
    gap: 5px;
    margin-top: 10px;
    justify-content: space-between;
  }
</style>
