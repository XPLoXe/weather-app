<template>
  <div></div>
</template>

<script setup>
import axios from 'axios'
import { useRoute } from 'vue-router'

//https://openweathermap.org/api/one-call-api
const route = useRoute()
const APIKey = 'b1170961541492433b0b9317087c5639'

const getWeatherData = async () => {
  try {
    console.log(route.query.lat)
    console.log(route.query.lng)
    const weatherData = await axios.get(
      `https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=b1170961541492433b0b9317087c5639`
    )

    // cal current date & time
    const localOffset = new Date().getTimezoneOffset() * 60000
    const utc = weatherData.data.current.dt * 1000 + localOffset
    weatherData.data.currentTime = utc + 1000 * weatherData.data.timezone_offset

    // cal hourly weather offset
    weatherData.data.hourly.forEach((hour) => {
      const utc = hour.dt * 1000 + localOffset
      hour.currentTime = utc + 1000 * weatherData.data.timezone_offset
    })

    return weatherData
  } catch (error) {
    console.log(error)
  }
}

// const getWeatherAPI = async () => {
//   try {
//     console.log(route.query.lat)
//     console.log(route.query.lng)
//     const weatherData = await axios.get(
//       `http://api.weatherapi.com/v1/current.json?key=b25b84160ae541ecb4a80024233008?q=Paris`
//     )

//     // cal current date & time
//     const localOffset = new Date().getTimezoneOffset() * 60000
//     const utc = weatherData.data.current.dt * 1000 + localOffset
//     weatherData.data.currentTime = utc + 1000 * weatherData.data.timezone_offset

//     // cal hourly weather offset
//     weatherData.data.hourly.forEach((hour) => {
//       const utc = hour.dt * 1000 + localOffset
//       hour.currentTime = utc + 1000 * weatherData.data.timezone_offset
//     })

//     return weatherData
//   } catch (error) {
//     console.log(error)
//   }
// }

const weatherData = await getWeatherData()
console.log(weatherData)
</script>
