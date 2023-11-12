<script setup>
import DaysWeather from "./DaysWeather.vue"
import axios from "axios"
import { onMounted, ref, watch } from 'vue'

const temperature = ref('')
const description = ref('')
const iconUrl = ref('')
const date = ref('')
const time = ref('')
const name = ref('')
const wind = ref('')
const country = ref('')
const humidity = ref('')
const error = ref('');

const months = [
  "January", "February", "March", "April", "May", "June",
  "July", "August", "September", "October", "November", "December"
];

const props = defineProps({
  city: {
    type: String,
    required: true
  }
})
const { VITE_OPENWEATHER_API_KEY } = import.meta.env

const fetchData = async () => {
  try {
    const url = `https://api.openweathermap.org/data/2.5/weather?q=${props.city}&units=metric&appid=${VITE_OPENWEATHER_API_KEY}`
    const response = await axios.get(url)

    if (response.status !== 200) {
      throw new Error(`Request failed with status ${response.status}`);
    }

    const weatherData = response.data
    name.value = weatherData.name
    temperature.value = Math.round(weatherData.main.temp)
    description.value = weatherData.weather[0].description
    wind.value = weatherData.wind.speed
    country.value = weatherData.sys.country
    humidity.value = weatherData.main.humidity
    const d = new Date()
    date.value = d.getDate() + '-' + months[d.getMonth()] + '-' + d.getFullYear()
    time.value = d.getHours() + ':' + d.getMinutes() + ':' + d.getSeconds()
    iconUrl.value = `https://api.openweathermap.org/img/w/${weatherData.weather[0].icon}.png`

    error.value = '';
  } catch (err) {
    // Handle errors
    console.error('Error fetching weather data:', err.message);
    error.value = 'An error occurred while fetching weather data.';
  }
}


watch(() => props.city, () => {
  if (props.city) {
    fetchData();
  }
}, { immediate: true });

onMounted(() => {
  fetchData()
})
</script>

<template>
  <div class="container p-0">
    <div class="d-flex">
      <div class="card main-div w-100">
        <div class="p-3">
          <h2 class="mb-1 day">Today</h2>
          <p class="text-light date mb-0">{{ date }}</p>
          <small>{{ time }}</small>
          <h2 class="place"><i class="fa fa-location"></i>{{ name.toUpperCase() }} <small>{{ country }}</small></h2>
          <div class="temp">
            <h1 class="weather-temp">{{ temperature }}&deg;c</h1>
            <h2 class="text-light">{{ description }} <img :src="iconUrl"></h2>
          </div>
        </div>
      </div>
      <div class="card card-2 w-100">
        <table class="m-4">
          <tbody>
            <tr>
              <th>Humidity</th>
              <th>{{ humidity }}</th>
            </tr>
            <tr>
              <th>Wind</th>
              <th>{{ wind }}</th>
            </tr>
          </tbody>
        </table>
        <DaysWeather :city-name="city" />

      </div>
    </div>

  </div>
</template>

<style scoped>
.weather-temp {
  margin: 0;
  font-weight: 700;
  font-size: 4em;
}

h2.mb-1.day {
  font-size: 3rem;
  font-weight: 400;
}

.main-div {
  border-radius: 20px;
  color: #fff;
  background-image: url(https://images.unsplash.com/photo-1682687218147-9806132dc697?q=80&w=1375&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDF8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D);
  background-size: cover;
  background-position: center;
  background-blend-mode: overlay;
  background-color: rgba(0, 0, 0, 0.5);
  background-repeat: no-repeat;
}

.temp {
  position: absolute;
  bottom: 0;
}

.main-div:hover {
  transform: scale(1.1);
  transition: transform 0.5s ease;
  z-index: 1;
}

.card-2 {
  background-color: #212738;
  border-radius: 20px;
  padding-bottom: 20px;
}


table {
  position: relative;
  left: 15px;
  border-collapse: separate;
  border-spacing: 15px;
  width: 85%;
  text-align: left;
  max-width: 600px;
  margin: 0 auto;
}

th,
td {
  font-size: 18px;
  color: #fff;
}

td {
  text-align: right;
}

table,
tr:hover {
  color: red;
}
</style>
