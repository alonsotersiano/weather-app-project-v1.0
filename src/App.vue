<template>
  <div id="main" :class="isDay ? 'day' : 'night'">
    <div class="container my-5" style="width: 385px; min-width: 350px">
      <h1 class="title text-center">Weather in</h1>
      <form class="search-location" v-on:submit.prevent="getWeather">
        <input
          type="text"
          class="form-control text-muted form-rounded p-4 shadow-sm"
          placeholder="What City?"
          v-model="citySearch"
          autocomplete="off"
        />
      </form>
        <p class="text-center my-3" v-if="cityFound">The city has not been found!</p>
      <div
        class="card my-3 shadow-lg back-card overflow-hidden"
        v-if="visible"
        style="max-width: 400px; min-width: 360px"
      >
        <div>
          <div icon="sunny" v-if="clearSky" data-label="Sunny">
            <span class="sun"></span>
          </div>

          <div icon="snowy" v-if="snowy" data-label="Chilly">
            <ul>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
            </ul>
          </div>

          <div icon="stormy" v-if="stormy" data-label="Soggy">
            <span class="cloud"></span>
            <ul>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
            </ul>
          </div>
          <div icon="cloudy" v-if="cloudy" data-label="Perfect">
            <span class="cloud"></span>
            <span class="cloud"></span>
          </div>
          <div icon="nightmoon" v-if="clearNight" data-label="Cool!">
            <span class="moon"></span>
            <span class="meteor"></span>
          </div>
        </div>

        <div class="card-top text-center" style="margin-bottom: 15rem">
          <div class="city-name my-3">
            <p>{{ weather.cityName }}</p>
            <span>...</span>
            <p class="">{{ weather.country }}</p>
          </div>
        </div>
        <div class="card-body">
          <div class="card-mid">
            <div class="row">
              <div class="col-12 text-center temp">
                <span>{{ weather.temperature }}&deg;C</span>
                <p class="my-4">{{ weather.description }}</p>
              </div>
            </div>
            <div class="row">
              <div class="col d-flex justify-content-between px-5 mx-5">
                <p>
                  {{ weather.lowTemp }}&deg;C
                </p>
                <p>
                  {{ weather.highTemp }}&deg;C
                </p>
              </div>
            </div>
          </div>
          <div class="card-bottom px-5 py-4 row">
            <div class="col text-center">
              <p>{{ weather.feelsLike }}&deg;C</p>
              <span>Feels like</span>
            </div>
            <div class="col text-center">
              <p>{{ weather.humidity }}%</p>
              <span>humidity</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  data() {
    return {
      cityFound: false,
      visible: false,
      snowy: false,
      clearNight: false,
      clearSky: false,
      cloudy: false,
      stormy: false,
      isDay: true,
      citySearch: "",
      weather: {
        cityName: "",
        country: "",
        temperature: null,
        description: "",
        lowTemp: "",
        highTemp: "",
        feelsLike: "",
        humidity: ""
      }
    }
  },
  methods: {
    getWeather: async function () {
      const key = "5299e83c9cf2fef510fb8ddaa3206ac8";
      const baseURL = `http://api.openweathermap.org/data/2.5/weather?q=${this.citySearch}&appid=${key}&units=metric`

      try {
        const response = await fetch(baseURL);
        const data = await response.json();
        this.weather.cityName = data.name;
        this.weather.country = data.sys.country;
        this.weather.temperature = Math.round(data.main.temp);
        this.weather.description = data.weather[0].description;
        this.weather.lowTemp = Math.round(data.main.temp_min);
        this.weather.highTemp = Math.round(data.main.temp_max);
        this.weather.feelsLike = Math.round(data.main.feels_like);
        this.weather.humidity = data.main.humidity;

        const timeOfDay = data.weather[0].icon;

        if (timeOfDay.includes("n")){
          this.isDay = false;
        }else{
          this.isDay = true;
        }
    
        const mainWeather = data.weather[0].main

        if(mainWeather.includes("Clouds")){
          this.snowy = false;
          this.clearNight = false;
          this.clearSky = false;
          this.cloudy = true;
          this.stormy = false;
        }

        if(mainWeather.includes("Snow")){
          this.snowy = true;
          this.clearNight = false;
          this.clearSky = false;
          this.cloudy = false;
          this.stormy = false;
        }

        if(mainWeather.includes("Thunderstorm") || mainWeather.includes("Rain")){
          this.snowy = false;
          this.clearNight = false;
          this.clearSky = false;
          this.cloudy = false;
          this.stormy = true;
        }

        if(mainWeather.includes("Clear") && this.isDay){
          this.snowy = false;
          this.clearNight = false;
          this.clearSky = true;
          this.cloudy = false;
          this.stormy = false;
        }

        if(mainWeather.includes("Clouds") && !this.isDay){
          this.snowy = false;
          this.clearNight = true;
          this.clearSky = false;
          this.cloudy = false;
          this.stormy = false;
        }

        this.citySearch = "";
        this.visible = true;
        this.cityFound = false;
      } catch (error) {
        console.log(error);
        this.cityFound = true;
        this.visible = false;
      }
    }
  }
}
</script>

<style>
@import "./assets/custom.css";
@import "./assets/animation.css";
</style>
