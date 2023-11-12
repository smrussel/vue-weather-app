<script setup>
import { ref, nextTick, watch } from 'vue'
import Weather from "./components/Weather.vue"

const city = ref('');
const showWeather = ref(false);
const isLoading = ref(false);

const searchWeather = async () => {
  if (city.value.trim() === '') {
    showWeather.value = false;
  } else {
    isLoading.value = true;
    showWeather.value = false;
    await nextTick();
    showWeather.value = true;
    isLoading.value = false;
  }
}

watch(city, (newCity) => {
  if (newCity.trim() === '') {
    showWeather.value = false;
  }
});

</script>

<template>
  <div class="app">
    <div class="header container h-100 p-5">
      <h1 class="mb-5">Weather Forecast</h1>
      <div class="d-flex justify-content-center h-100">
        <div class="searchbar w-50 mx-2">
          <input type="text" v-model="city" class="input form-control" placeholder="Enter a city"
            @keyup.enter="searchWeather">
        </div>
        <button class="btn-search btn btn-primary" @click="searchWeather">Search <i class="fas fa-search"></i></button>
      </div>
      <div v-if="!showWeather && !city" class="d-flex text-white justify-content-center align-items-center mt-3">Please
        enter a city name.</div>
    </div>
    <br>
    <div v-if="isLoading" class="loading-icon">
      <span>Loading...</span>
    </div>

    <Weather :city="city" :key="city" v-if="showWeather" />
  </div>
</template>

<style>
.header {
  background-color: #212730;
  border-radius: 20px;
  color: #fff;
  text-align: center;
  font-family: 'Franklin Gothic Medium', 'Arial Narrow', Arial, sans-serif;
  margin-top: 5rem;
}

.btn-search {
  background-image: linear-gradient(to right, #24C6DC 0%, #514A9D 51%, #24C6DC 100%);
  transition: 0.5s;
  background-size: 200% auto;
  color: white;
}

.btn-search:hover {
  background-position: right center;
  color: #fff;
  text-decoration: none;
}
</style>
