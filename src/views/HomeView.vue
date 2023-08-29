<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input
        type="text"
        v-model="searchQuery"
        @input="getSearchResults"
        placeholder="Search for a city or state"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
      />
      <ul
        class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
        v-if="mapboxSearchResults"
      >
        <li
          v-for="searchResult in mapboxSearchResults"
          :key="searchResult.iataCode"
          class="py-2 cursor-pointer"
        >
          {{ searchResult.name }} ({{ searchResult.address.countryCode }})
        </li>
      </ul>
    </div>
    <div class="py-20"></div>
  </main>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'

// const mapboxAPIKey =
//   'pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFz0DZvMHJkaDJ1bWx60GVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q'
// const searchQuery = ref('')
// const queryTimeout = ref(null)
// const mapboxSearchResults = ref(null)

// const getSearchResults = () => {
//   clearTimeout(queryTimeout.value)
//   queryTimeout.value = setTimeout(async () => {
//     if (searchQuery.value !== '') {
//       const result = await axios.get(
//         `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}`
//       )
//       mapboxSearchResults.value = result.data.features
//       console.log(mapboxSearchResults.value)
//       return
//     }
//     mapboxSearchResults.value = null
//   }, 300)
// }

const apiKey = 'EZ9AtUGWJtOLJG9Fvqi9CKp8gzlEFs4d'
const apiSecret = 'DUMHhESgvsx6FGwh'
const searchQuery = ref('')
const queryTimeout = ref(null)
const mapboxSearchResults = ref(null)

const getSearchResults = () => {
  clearTimeout(queryTimeout.value)
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== '') {
      const tokenResponse = await axios.post(
        'https://test.api.amadeus.com/v1/security/oauth2/token',
        `grant_type=client_credentials&client_id=${apiKey}&client_secret=${apiSecret}`,
        {
          headers: {
            'Content-Type': 'application/x-www-form-urlencoded'
          }
        }
      )

      const accessToken = tokenResponse.data.access_token

      const result = await axios.get(
        `https://test.api.amadeus.com/v1/reference-data/locations/cities?keyword=${searchQuery.value}&max=5`, // Replace with the actual API endpoint
        {
          headers: {
            Authorization: `Bearer ${accessToken}`
          }
        }
      )

      mapboxSearchResults.value = result.data.data
      console.log(mapboxSearchResults.value)
      console.log(mapboxSearchResults.value[0].name)
      return
    }
    mapboxSearchResults.value = null
  }, 300)
}
</script>
