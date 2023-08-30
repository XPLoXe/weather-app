<template>
  <div>
    <Suspense>
      <AsyncCityView></AsyncCityView>
      <template #fallback>
        <p class="flex flex-col items-center text-white">Loading...</p>
      </template>
    </Suspense>
  </div>
</template>

<script setup>
import AsyncCityView from '../components/AsyncCityView.vue'
</script>
