<script setup>
import { onMounted, ref, watch } from 'vue'
import axios from "axios"
import moment from "moment"

const props = defineProps({
  cityName: {
    type: String,
    required: true
  }
})

const forecast = ref([])
const loading = ref(true)
const error = ref('');

const { VITE_OPENWEATHER_API_KEY } = import.meta.env

const fetchWeatherData = async () => {
  try {
    const url = `https://api.openweathermap.org/data/2.5/forecast?q=${props.cityName}&units=metric&appid=${VITE_OPENWEATHER_API_KEY}`
    const response = await axios.get(url)

    if (response.status !== 200) {
      throw new Error(`Request failed with status ${response.status}`);
    }

    const forecastData = response.data.list
    forecast.value = forecastData.map(item => {
      return {
        date: moment(item.dt_txt.split(' ')[0]),
        temperature: Math.round(item.main.temp),
        description: item.weather[0].description,
        iconUrl: `https://api.openweathermap.org/img/w/${item.weather[0].icon}.png`
      };
    }).reduce((acc, item) => {
      if (!acc.some(day => day.date.isSame(item.date, 'day'))) {
        acc.push(item);
      }
      return acc;
    }, []).slice(1, 5);

    loading.value = false;
    error.value = '';
  } catch (err) {
    // Handle errors
    console.error('Error fetching weather data:', err.message);
    error.value = 'An error occurred while fetching weather data.';
  }
}

const getDayName = (date) => {
  return date.format('ddd')
}

watch(() => props.cityName, () => {
  if (props.cityName) {
    fetchWeatherData();
  }
}, { immediate: true });

onMounted(() => {
  fetchWeatherData()
})
</script>

<template>
  <div class="days-tab text-center">
    <div v-if="loading" class="loading">Loading...</div>
    <ul v-else class="p-0">
      <li v-for="day in forecast" :key="day.date" class="li_active">
        <div class="py-3"><img :src="day.iconUrl"></div>
        <div class="py-3">{{ getDayName(day.date) }}</div>
        <div class="py-3">{{ day.temperature }}&deg;c</div>
      </li>
    </ul>
  </div>
</template>

<style scoped>
.days-tab {
  width: 90%;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
  border-radius: 20px;
  width: 90%;
  margin: auto;
}

.loading {
  color: #fff;
}

ul {
  margin: 0;
}

li {
  display: inline-block;
  list-style: none;
  height: 100%;
  width: 21%;
  max-width: 21%;
  font-size: 1vw;
  line-height: 1.2;
}

span {
  display: block;
  margin-bottom: 5px;
  font: 100% sans-serif;
  height: 35px;
}

.li_active {
  background: #253d5c;
  color: #222831;
  border-radius: 10px;
  margin: 0.5rem;
  color: #fff;
  font-weight: 600;
}

.li_active:hover {
  transform: scale(1.2);
  transition: transform 0.1s ease;
}
</style>
