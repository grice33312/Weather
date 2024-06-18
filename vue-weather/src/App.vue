<script setup>
import { ref , onMounted , computed } from 'vue';
import axios from 'axios';
import format from 'date-fns/format';
const api_key = import.meta.env.VITE_API_KEY;
const base_url = 'https://api.openweathermap.org';
const query = ref("Taipei")  ; 
const limit = 1;
const weather = ref({})
let city = ref({});


const axiosLocation = async () => {
  try {
    const result = await axios.get(`${base_url}/geo/1.0/direct?q=${query.value}&limit=${limit}&appid=${api_key}`);
    city.value = result.data ;
    const lat = city.value[0].lat
    const lon = city.value[0].lon
    await axiosWeather(lat , lon);
    console.log()
  } catch (error) {
    console.log('Error fetching weather data:', error)
  }
}

const axiosWeather = async (lat , lon) => {
  try {
    const response = await axios.get(`${base_url}/data/2.5/weather?lat=${lat}&lon=${lon}&appid=${api_key}`);
    weather.value = response.data;
  } catch (error) {
    console.error('Error fetching weather data:', error);
  }
};

const currentDate = ref(format(new Date(),"MMMM do yyyy"))


onMounted(() => {
  axiosLocation();
});

console.log(weather.value)

</script>

<template>
  <div id="app"v-for="(item , index) in city">
    <div class="container" >
      <!-- search bar -->
      <div class="search-box">
        <input type="text" placeholder="Search...." class="search-bar" v-model="query" @keyup.enter="axiosLocation"/>
      </div>
      <div class="weather-wrapper" >
        <!-- location & date info -->
        <div class="location-box">

          <div class="location">{{ item.name }}</div>
          <div class="date">{{currentDate }}</div>
        </div>

        <!-- weather info -->
        <div class="weather-box">
          <div class="temperature">{{ Math.round(weather.main.temp) }}</div>
          <div class="weather">{{ weather.weather[0].main }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

#app {
  background-image: url('./assets/cold-bg.jpg');
  background-position: 40%;
  transition: 0.5s;
}

#app.warm {
  background-image: url('./assets/warm-bg.jpg');
}

.container {
  height: 100vh;
  padding: 25px;
  background-image: linear-gradient(to bottom,
      rgba(0, 0, 0, 0.25),
      rgba(0, 0, 0, 0.75))
}

.search-box {
  width: 100%;
  margin-bottom: 35px;
}

.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;
  transition: all 0.4s ease-in;
  border-radius: 0 16px 0 16px;
  font-size: 20px;
  border: none;
  outline: none;
  background: none;
  background-color: hsla(0, 0%, 100%, .5);
}

.search-box .search-bar:focus {
  background-color: rgba(255, 255, 255, 0.75);
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  border-radius: 16px 0px 16px 0px;
}

.location-box .location {
  color: #fff;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  color: #fff;
  font-size: 20px;
  font-weight: 300;
  text-align: center;
  font-style: italic;
}

.weather-box {
  text-align: center;
  margin-top: 15px;
}

.weather-box .temperature {
  display: inline-block;
  padding: 10px 25px;
  color: #fff;
  font-size: 102px;
  font-weight: 900;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .weather {
  font-size: 48px;
  color: #fff;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
</style>
