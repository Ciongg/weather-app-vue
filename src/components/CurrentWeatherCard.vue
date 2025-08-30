<script setup>
import { ref, onMounted } from "vue"
import {weatherCodeMap, weatherIconMap} from '../utils/weatherMaps.js'

const currentWeatherData = ref(null)

async function fetchCurrentWeather() {
  const res = await fetch("https://api.open-meteo.com/v1/forecast?latitude=14.6042&longitude=120.9822&current_weather=true")
  if (!res.ok) throw new Error('error fetching current weather')
  const data = await res.json()
  currentWeatherData.value = data.current_weather
}

function getWeatherIcon(weatherCode) {
    const iconname = weatherIconMap[weatherCode] || 'clear'
    return new URL(`../assets/icons/day/${iconname}.svg`, import.meta.url).href
}

onMounted(() => {
  fetchCurrentWeather()
})
</script>

<template>
  <div v-if="currentWeatherData" class="current-weather-card">
    <div>
      <h2>Current Weather</h2>
    </div>
    <div class="current-weather-header">

        <div>
          <img :src="getWeatherIcon(currentWeatherData.weathercode)" alt="">
        </div>

        <h2>{{ weatherCodeMap[currentWeatherData.weathercode] }}</h2>
    </div>
    
    <div class="current-weather-details">

      <div>Temperature: {{ currentWeatherData.temperature }}°C</div>
      <div>Wind Speed: {{ currentWeatherData.windspeed }}km/h</div>
      <div>Weather Code: {{ currentWeatherData.weathercode }}</div>
    </div>
  </div>
</template>

<style>
.current-weather-card {
  /* Custom styles for current weather card */
  box-shadow: 0 4px 12px rgba(0,0,0,0.15);
  padding: 16px;
  margin: 16px;
  height: 250px;
  background: rgba(255, 255, 255, 0.5);
  border-radius: 8px;
  display: flex;
  flex-direction: column;
  justify-content: space-around;
}

.current-weather-card img{
  height: 64px;
  width: 64px;
}

.current-weather-header{
  display: flex;
  align-items: center;
  gap: 16px;
  font-size: 1.2em;
  margin-bottom: 8px;
}

.current-weather-details{
  display: flex;
  justify-content: start;
  gap: 24px;
}
</style>