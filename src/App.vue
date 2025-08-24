<script setup>
import { ref, onMounted } from "vue"

const dailyWeather = ref([])
const dailyWeatherTime = ref([])
const loading = ref(true)
const error = ref(null)

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
  96: "Thunderstorm with hail: Slight",
  99: "Thunderstorm with hail: Heavy"
}

async function fetchWeather() {
  try {
    const res = await fetch(
      "https://api.open-meteo.com/v1/forecast?latitude=14.6042&longitude=120.9822&daily=weather_code&timezone=auto"
    )
    if (!res.ok) throw new Error("Failed to fetch weather data")

    const data = await res.json()
    dailyWeather.value = data.daily.weather_code
    dailyWeatherTime.value = data.daily.time
  } catch (err) {
    error.value = err.message
  } finally {
    loading.value = false
  }
}

onMounted(() => {
  fetchWeather()
})
</script>

<template>
  <div>
    <h2>Weather Forecast</h2>

    <p v-if="loading">Loading...</p>
    <p v-else-if="error">{{ error }}</p>
    <ul v-else>
      <li v-for="(code, index) in dailyWeather" :key="index">
        {{ weatherCodeMap[code] || "Unknown" }}
        - {{ dailyWeatherTime[index] }}
      </li>
    </ul>
  </div>
</template>
