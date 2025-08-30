<script setup>
import { ref, onMounted } from "vue"
import {weatherCodeMap, weatherIconMap} from '../utils/weatherMaps.js'

const weatherData = ref([])
const showBody = ref([])

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
  return new URL(`../assets/icons/day/${iconName}.svg`, import.meta.url).href
}

  onMounted(()=>{
    fetchWeather()
  })


  function toggleBody(index){
    if(showBody.value.includes(index)){
      showBody.value = showBody.value.filter(i => i !== index)
    } else {
      showBody.value.push(index)
    }

  }



</script>

<template>

  <h1>Weather Forecast 7 days</h1>
  <div class="weather-container">

    <div class="weather-card" v-for="(dailyWeather, index) in weatherData" :key="index">
      
      <div class="weather-header">
      <div class="weather-header-content" @click="toggleBody(index)">
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
      
      <div v-if="showBody.includes(index)" class="weather-body" >
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

  .weather-container{
    display: flex;
  }

  .weather-card{
    display: flex;
    flex-direction: column;
    box-shadow: 0 4px 8px rgba(0,0,0,0.1);
    padding: 10px;
    margin: 10px;
    width: auto;
    cursor: pointer;
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
    cursor: pointer;
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
