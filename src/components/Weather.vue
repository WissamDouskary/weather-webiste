<template>
    <div class="h-screen w-screen bg-gradient-to-br from-teal-200 to-teal-600 flex flex-col">
      <header class="bg-white bg-opacity-90 shadow-md py-4 px-8">
        <div class="w-full px-8 flex flex-col sm:flex-row items-center justify-center sm:justify-between gap-6">
          <h1 class="text-3xl font-bold text-gray-800 flex items-center">
            <span class="text-4xl">🌦️</span> Weather
          </h1>
          <div class="w-96 relative">
            <input 
              v-model="city" 
              @keyup.enter="getWeather" 
              placeholder="Enter city name (Enter to Search)" 
              class="w-full p-3 pl-5 pr-12 border border-gray-200 rounded-full text-gray-700 bg-gray-50 focus:outline-none focus:ring-2 focus:ring-teal-400 focus:bg-white transition-all duration-200"
            />
          </div>
        </div>
      </header>
  
      <main class="flex-1 overflow-auto p-6">
        <div class="mx-auto">
          <div v-if="loading" class="flex justify-center items-center h-64">
            <div class="animate-pulse text-white text-2xl">Loading weather data...</div>
          </div>
          
          <div v-if="error" class="bg-red-50 text-red-600 p-6 rounded-xl text-center max-w-lg mx-auto mt-8">
            <p class="text-lg">{{ error }}</p>
          </div>
  
          <div v-if="weather && !loading" class="grid grid-cols-1 lg:grid-cols-3 gap-8">
            
            <div class="bg-white rounded-2xl shadow-xl p-8 h-full">
              <div class="flex flex-col items-center">
                <h2 class="text-4xl font-bold text-gray-800">{{ weather.name }}</h2>
                <div class="text-7xl font-bold my-6 text-teal-600">{{ weather.main.temp }}°C</div>
                <p class="capitalize text-2xl font-medium text-gray-700 mb-8">{{ weather.weather[0].description }}</p>
                
                <div class="grid grid-cols-2 gap-6 w-full mt-4">
                  <div class="bg-gray-50 p-6 rounded-xl">
                    <p class="text-gray-500 text-sm mb-1">Humidity</p>
                    <p class="text-gray-800 font-medium text-xl">{{ weather.main.humidity }}%</p>
                  </div>
                  <div class="bg-gray-50 p-6 rounded-xl">
                    <p class="text-gray-500 text-sm mb-1">Wind Speed</p>
                    <p class="text-gray-800 font-medium text-xl">{{ weather.wind.speed }} km/h</p>
                  </div>
                </div>
  
                <button @click="togglemodal" class="w-full mt-8 py-4 rounded-xl text-white bg-teal-500 hover:bg-teal-600 transition-all duration-200 text-lg font-medium">
                  {{ showModal ? 'Hide Forecast' : 'Show Forecast' }}
                </button>
              </div>
            </div>
            
            <div v-if="showModal && forecast" class="lg:col-span-2 bg-white rounded-2xl shadow-xl p-8">
              <h3 class="text-2xl font-semibold text-gray-800 mb-6">Forecast: Next 24 Hours</h3>
              <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
                <div v-for="fore in forecast.list.slice(0, 8)" :key="fore.dt" class="bg-gray-50 p-4 rounded-xl">
                  <div class="flex flex-col items-center">
                    <p class="text-gray-500 font-medium">{{ new Date(fore.dt * 1000).toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'}) }}</p>
                    <p class="text-teal-600 font-bold text-2xl my-2">{{ fore.main.temp }} °C</p>
                    <p class="capitalize text-gray-700">{{ fore.weather[0].description }}</p>
                    <div class="mt-2 grid grid-cols-2 gap-2 w-full text-sm">
                      <div>
                        <span class="text-gray-500">Humidity:</span>
                        <span class="text-gray-700"> {{ fore.main.humidity }}%</span>
                      </div>
                      <div>
                        <span class="text-gray-500">Wind:</span>
                        <span class="text-gray-700"> {{ fore.wind.speed }} km/h</span>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </main>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue'
  
  const showModal = ref(true)
  
  const togglemodal = () => {
      showModal.value = !showModal.value
  }
  
  const city = ref('')
  const loading = ref(false)
  const weather = ref(null)
  const error = ref(null)
  const forecast = ref(null)
  
  const API_KEY = 'c27cfba05cedcac6ec9766bf58b2aea0'
  
  const getWeather = async () => {
      if (!city.value) return
      loading.value = true
      error.value = ''
  
      try {
          const weatherResponse = await fetch(`https://api.openweathermap.org/data/2.5/weather?q=${city.value}&units=metric&appid=${API_KEY}`)
          if (!weatherResponse.ok) throw new Error('City not found or API error')
          weather.value = await weatherResponse.json()
  
          const forecastResponse = await fetch(`https://api.openweathermap.org/data/2.5/forecast?q=${city.value}&units=metric&appid=${API_KEY}`);
          if (!forecastResponse.ok) throw new Error('forecast not found!')
          forecast.value = await forecastResponse.json()
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
  
  body {
  margin: 0;
  padding: 0;
  min-height: 100vh;
}

  </style>