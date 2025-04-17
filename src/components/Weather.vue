<template>
    <div class="min-h-screen flex items-center justify-center bg-gradient-to-br from-teal-200 to-teal-600 p-4">
      <div class="bg-white shadow-xl rounded-3xl p-6 w-full max-w-md">
        <div class="text-center mb-6">
          <h1 class="text-2xl font-bold text-gray-800 flex items-center justify-center gap-2">
            <span class="text-3xl">🌦️</span> Weather App
          </h1>
        </div>
  
        <div class="relative mb-6">
          <input 
            v-model="city" 
            @keyup.enter="getWeather" 
            placeholder="Enter city name" 
            class="w-full p-3 pl-4 pr-10 border border-gray-200 rounded-full text-gray-700 bg-gray-50 focus:outline-none focus:ring-2 focus:ring-teal-400 focus:bg-white transition-all duration-200 w-96"
          />
          <div class="text-xs text-gray-500 mt-1 text-center">Press Enter to search</div>
        </div>
  
        <div v-if="loading" class="flex justify-center my-8">
          <div class="animate-pulse text-teal-600 text-lg">Loading weather data...</div>
        </div>
  
        <div v-if="weather && !loading" class="mt-6 bg-gray-50 rounded-2xl p-5 transition-all duration-300">
          <div class="flex flex-col items-center">
            <h2 class="text-2xl font-bold text-gray-800">{{ weather.name }}</h2>
            <div class="text-4xl font-bold my-3 text-teal-600">{{ weather.main.temp }}°C</div>
            <p class="capitalize text-lg font-medium text-gray-700 mb-4">{{ weather.weather[0].description }}</p>
            
            <div class="grid grid-cols-2 gap-4 w-full mt-2">
              <div class="bg-white p-3 rounded-xl shadow-sm">
                <p class="text-gray-500 text-sm">Humidity</p>
                <p class="text-gray-800 font-medium">{{ weather.main.humidity }}%</p>
              </div>
              <div class="bg-white p-3 rounded-xl shadow-sm">
                <p class="text-gray-500 text-sm">Wind Speed</p>
                <p class="text-gray-800 font-medium">{{ weather.wind.speed }} km/h</p>
              </div>
            </div>
          </div>
        </div>
  
        <div v-if="error" class="mt-6 bg-red-50 text-red-600 p-4 rounded-xl text-center">
          <p>{{ error }}</p>
        </div>
      </div>
    </div>
  </template>

<script setup>
import { ref } from 'vue'

const city = ref('')
const loading = ref(false)
const weather = ref(null)
const error = ref(null)

const API_KEY = 'c27cfba05cedcac6ec9766bf58b2aea0'

const getWeather = async () => {
    if (!city.value) return
    loading.value = true
    error.value = ''

    try {
        const weatherResponse = await fetch(`https://api.openweathermap.org/data/2.5/weather?q=${city.value}&units=metric&appid=${API_KEY}`)
        if (!weatherResponse.ok) throw new Error('City not found or API error')
        weather.value = await weatherResponse.json()
    }
    catch (err) {
        error.value = err.message
        weather.value = null
    }
    finally {
        loading.value = false
    }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700&display=swap');

* {
  font-family: 'Poppins', sans-serif;
}
</style>