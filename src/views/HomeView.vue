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
        v-if="
          typeof mapboxSearchResults === 'undefined' ||
          mapboxSearchResults ||
          (searchQuery.length < 3 && searchQuery.length > 0)
        "
      >
        <p class="py-2" v-if="searchError">Sorry, something went wrong, please try again</p>

        <p
          class="py-2"
          v-if="
            (!searchError && typeof mapboxSearchResults === 'undefined') || searchQuery.length < 3
          "
        >
          No results match your query, try a different term
        </p>
        <template v-else>
          <li
            v-for="searchResult in mapboxSearchResults"
            :key="searchResult.iataCode"
            class="py-2 cursor-pointer"
            @click="previewCity(searchResult)"
          >
            {{ searchResult.name }} ({{ searchResult.address.countryCode }})
          </li>
        </template>
      </ul>
    </div>
    <div class="py-20"></div>
  </main>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'
import { useRouter } from 'vue-router'

const router = useRouter()
const previewCity = (searchResult) => {
  router.push({
    name: 'cityView',
    params: {
      country: searchResult.address.countryCode,
      city: searchResult.name.replaceAll(' ', '')
    },
    query: {
      lat: searchResult.geoCode.latitude,
      lng: searchResult.geoCode.longitude,
      preview: true
    }
  })
}

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
const searchError = ref(false)

const getSearchResults = () => {
  clearTimeout(queryTimeout.value)
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value.length > 2) {
      try {
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

        //https://developers.amadeus.com/self-service/category/destination-experiences/api-doc/city-search/api-reference
        const result = await axios.get(
          `https://test.api.amadeus.com/v1/reference-data/locations/cities?keyword=${searchQuery.value}&max=5`, // Replace with the actual API endpoint
          {
            headers: {
              Authorization: `Bearer ${accessToken}`
            }
          }
        )

        mapboxSearchResults.value = result.data.data
        searchError.value = false
      } catch (error) {
        if (error.response) {
          console.log('primer error')
          if (error.response.data.code === 4926) {
            console.error('Error: INVALID DATA RECEIVED')
            console.error('Detail:', error.response.data.detail)
            console.log('error enter')
          }
        }
        searchError.value = true
      }

      return
    }
    mapboxSearchResults.value = null
  }, 300)
}
</script>
