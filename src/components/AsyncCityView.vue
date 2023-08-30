<template>
  <div></div>
</template>

<script setup>
import axios from 'axios'
import { useRoute } from 'vue-router'

//https://openweathermap.org/api/one-call-api
const route = useRoute()
//const APIKey = 'b1170961541492433b0b9317087c5639'

const getWeatherData = async () => {
  try {
    console.log(route.query.lat)
    console.log(route.query.lng)
    // const weatherData = await axios.get(
    //   `https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude=hourly,daily&appid=b1170961541492433b0b9317087c5639`
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
console.log(weatherData)
</script>
