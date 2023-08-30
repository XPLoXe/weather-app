<template>
  <div class="flex flex-col flex-1 items-center">
    <!-- Banner -->
    <div v-if="route.query.preview" class="text-white p-4 bg-weather-secondary w-full text-center">
      <p>You are currently previewing this city, click the "+" icon to start tracking this city</p>
    </div>
    <!-- Weather Overview -->
    <div class="flex flex-col items-center text-white py-12">
      <h1 class="text-6xl mb-2">{{ route.params.city }}</h1>
      <h2 class="text-2xl mb-2">{{ formattedTime }}</h2>
      <h3 class="text-xl mb-2">(Local time: {{ formattedLocalTime }})</h3>
      <p class="text-md mb-2">Your time:</p>
      <p class="text-md mb-8">
        {{ formattedHour }}
      </p>
      <p class="text-6xl mb-8">
        {{ sky }}
      </p>
      <p class="text-8xl mb-8">
        {{ temperature }}
      </p>
    </div>
  </div>
</template>

<script setup>
import axios from 'axios'
import { useRoute } from 'vue-router'
import { ref, onMounted, onBeforeUnmount } from 'vue'
const route = useRoute()

// LIFE CYCLES \\
let timer
let timer2
onMounted(() => {
  //update the time every second
  timer = setInterval(updateCurrentTime, 1000)
  updateCurrentLocalTime()
  timer2 = setInterval(updateCurrentLocalTime, 1000 * 60)
})
onBeforeUnmount(() => {
  clearInterval(timer)
  clearInterval(timer2)
})

// TIME \\
const currentTime = ref(new Date())
const formattedTime = ref('Loading . . .')
const formattedHour = ref('')

const updateCurrentTime = () => {
  // UPDATE LOCAL TIME \\
  currentTime.value = new Date()
  const dayOfWeek = currentTime.value.toLocaleString('en-US', { weekday: 'short' })
  const month = currentTime.value.toLocaleString('en-US', { month: 'short' })
  const day = currentTime.value.getDate()
  const year = currentTime.value.getFullYear()
  const hours = currentTime.value.getHours().toString().padStart(2, '0')
  const minutes = currentTime.value.getMinutes().toString().padStart(2, '0')
  const seconds = currentTime.value.getSeconds().toString().padStart(2, '0')

  formattedTime.value = `${dayOfWeek} ${month} ${day} ${year}`
  formattedHour.value = `${hours}:${minutes}:${seconds}`

  // UPDATE CITY TIME \\
}

// CITY LOCAL TIME \\
const formattedLocalTime = ref('Loading . . .')
const updateCurrentLocalTime = async () => {
  const localTime = ref(await getLocalTime())
  const dateLocalTime = ref(new Date(localTime.value))
  formattedLocalTime.value = dateLocalTime.value.toLocaleTimeString([], {
    hour: '2-digit',
    minute: '2-digit',
    hour12: true
  })
}

const getLocalTime = async () => {
  try {
    const localTime = await axios.get(
      `http://api.timezonedb.com/v2.1/get-time-zone?lat=${route.query.lat}&lng=${route.query.lng}&key=MO5YG3JTCOH0&by=position&format=json`
    )
    console.log(localTime.data.formatted)
    return localTime.data.formatted
  } catch (error) {
    console.log(error)
  }
}

// WEATHER \\

//https://openweathermap.org/api/one-call-api

//const APIKey = 'b1170961541492433b0b9317087c5639'

const getWeatherData = async () => {
  try {
    console.log(route.query.lat)
    console.log(route.query.lng)
    // const weatherData = await axios.get(
    //   `https://api.openweathermap.org/data/2.5/weather?lat=${route.query.lat}&lon=${route.query.lng}&exclude=hourly,daily&appid=b1170961541492433b0b9317087c5639`
    // )
    const weatherData = await axios.get(
      `https://wttr.in/${route.query.lat},${route.query.lng}?format=%C+%t`
    )

    return weatherData
  } catch (error) {
    console.log(error)
  }
}

const weatherData = await getWeatherData()
console.log(weatherData.data)

const parts = weatherData.data.split(/([-+]\d+°C)/)
const sky = parts[0].trim()
const temperature = parts[1].trim()
</script>
