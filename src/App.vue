<template>
  <div class="home">
    <!-- <img alt="Vue logo" src="./assets/logo_pokemon.png"> -->
  </div>
  <div class="counter">{{ random }}</div>
  <hr>
  <p>{{ currentDate }}</p>
  <div class="counter">
    <!-- {{ counter }} -->
    <!-- {{ currentDate }} -->
  </div>
  <div class="buttons">
    <!-- <input type="text" name="" id="" v-model="counter"> -->
    <!-- <button v-on:click="decreaseCounter" :disabled="counter <= 1">-</button>
    <button v-on:click="increaseCounter">+</button> -->
  </div>

  <div class="list-card" v-if="city">
    <select v-model="random" @change="getCordinates">
      <option v-for="todo, index in city" :key="index" :selected="index">{{ todo }}</option>
    </select>

    <!-- <div v-if="weatherData">
      <h3>{{ random }} </h3>
      {{ weatherData.description }}
      <p>Weather : {{ weatherData.temperature }}</p>
      <p>wind : {{ weatherData.wind }}</p>
      <table>
        <thead>
          <th>Day</th>
          <th>temperature</th>
          <th>wind</th>
        </thead>
        <tbody>
          <tr v-for="wa in weatherData.forecast">
            <td>{{ wa.day }}</td>
            <td>{{ wa.temperature }}</td>
            <td>{{ wa.wind }}</td>
          </tr>
        </tbody>
      </table>
    </div> -->
  </div>
  <!-- {{ weatherData }} -->

  <div class='box' v-if="weatherData"
    :style="{ 'backgroundColor': weatherData.main.temp_max > 25 ?( weatherData.main.temp_max >40 ? '#DE4444' : '#DEA944') : '#0E4DAC' }">
    <div class='wave -one'></div>
    <div class='wave -two'></div>
    <div class='wave -three'></div>
    <div class="weathercon">
      <i :style="{ 'color': weatherData.main.temp_max > 25 ? '#fff' : '#eff3f9' }" :class="weatherData.weather[0].main === 'Sunny' ? 'fas fa-cloud-sun' : (weatherData.weather[0].main === 'Clouds' ? 'fas fa-cloud' :(weatherData.weather[0].main === 'Rain' ? 'fas fa-bolt' : 'fas fa-cloud') ) "></i>
    <!-- <img :src="weatherData.weather[0].icon" /> -->
    </div>
    <div class="info">
      <h2 class="location" :style="{ 'color': weatherData.main.temp_max > 25 ? ( weatherData.main.temp_max >40 ? '#fff' : '#d36326') : '#eff3f9' }">{{ this.weatherData.name.toUpperCase() }}</h2>
      <p class="date">{{ currentDate }}</p>
      <p class="date">Pressure: {{ this.weatherData.main.pressure}}</p>
      <p class="date">Humidity:{{ this.weatherData.main.humidity }}%</p>
      <p class="date">Wind: {{ this.weatherData.wind.speed }}km/h</p>
      <p class="date" v-if="this.weatherData.main.sea_level">Sea Level: {{ this.weatherData.main.sea_level }}</p>
       <h1 class="temp" :style="{ 'color': weatherData.main.temp_max > 25 ?  ( weatherData.main.temp_max >40 ? '#fff' : '#d36326') : '#fff' }">{{ Math.round(this.weatherData.main.temp_max) +
        "&deg; C | " +
        Math.round(Math.round(this.weatherData.main.temp_max) * 1.8 + 32) +
        "&deg; F" }}</h1>
    </div>
  </div>


  <h1 v-if="emojiData" v-for="emo in emojiData" class="emoji">
    {{ emo.character }}

  </h1>
  <!-- pokemon -->
  <div class="list card" v-if="peoples">
    <select>
      <option v-for="todo, index in peoples" :disabled="todo.unavailable" :selected="index + 1 === counter"><a
          :href="todo.url">{{
            todo.name }}</a></option>
    </select>
    <div v-for="poke, index in peoples">
      <img
        :src="'https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/dream-world/' + (index + 1) + '.svg'"
        style="width:8%" />
      <label>{{ poke.name }}</label>
      <img class="object-cover" :src="'https://www.pkparaiso.com/imagenes/xy/sprites/animados/' + poke.name + '.gif'">
      <hr/>
    </div>
  </div>
  <!-- pokemon end -->
</template>
<script>

import axios from "axios";
export default {
  name: 'App',
  data() {
    return {
      counter: 1,
      random: "Select City",
      peoples: null,
      city: null,
      weatherData: null,
      emojiData: null,
      cordinates: null,
      temp: null,
      currentDate: null

    }
  },

  methods: {
    increaseCounter() {
      this.counter++;
      // this.pokeData();
    },
    decreaseCounter() {
      if (this.counter > 1) {
        this.counter--;
        // this.pokeData();
      }
      else {
        this.counter = 1;
      }
    },
    pokeData() {
      axios('https://pokeapi.co/api/v2/pokemon?limit=' + this.counter + '&offset=0').then(response => {
        this.peoples = response.data.results
      })
    },
    cityList() {
      axios.post('https://countriesnow.space/api/v0.1/countries/cities', {
        'country': 'india'
      }).then(response => {
        // console.log(response)
        this.city = response.data.data
      }).catch(error => {

      })

    },
    checkWeather() {
      axios(' https://goweather.herokuapp.com/weather/' + this.random
      ).then(response => {
        this.weatherData = response.data

      }).catch(error => {
        // console.log(error)

      })
    },
    emojiList() {
      axios('https://emoji-api.com/emojis?access_key=244090013f8370c49acbbbc0c78feedc2effddfe').then(response => {
        console.log(response)
        this.emojiData = response.data

      }).catch(error => {
        // console.log(error)

      })
    },
    getCordinates() {
      axios('http://api.positionstack.com/v1/forward?access_key=db11dca810dfe8e9a7b0063860d03a94&query=' + this.random).then(response => {
        this.cordinates = {
          latitude: response.data.data[0].latitude,
          longitude: response.data.data[0].longitude
        }
        this.getWeatherData()
      }).catch(error => {
        // console.log(error)
      })
    },
    getWeatherData() {
      axios('https://fcc-weather-api.glitch.me/api/current?lat=' + this.cordinates.latitude + '&lon=' + this.cordinates.longitude).then(response => {
        this.weatherData = response.data
      }).catch(error => {
        console.log(error)
      })
    }
  },
  computed: {
    temp() {
      if (!this.weatherData) {
        return "";
      }
      return (
        Math.round(this.weatherData.main.temp_max) +
        "&deg; C | " +
        Math.round(Math.round(this.weatherData.main.temp_max) * 1.8 + 32) +
        "&deg; F"
      );
    }
  },
  mounted() {
    // this.pokeData();
    this.cityList();
    // this.emojiList();
    setInterval(() => {
      const date = new Date()
      this.currentDate = date.toLocaleString('en-US', {
        weekday: 'long',
        month: 'short',
        day: 'numeric',
        hour: 'numeric',
        minute: 'numeric',
        hour12: true,
        timeZone: 'Asia/Kolkata'
      }).toUpperCase().replace(',', ' |')
    }, 1000)
  }
}
</script>

<style>
@import "https://use.fontawesome.com/releases/v5.3.1/css/all.css";
</style>


<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 30px;
}

/* div {
  margin-bottom: 1px;

} */
* {
  padding: 0;
  margin: 0;
  font-family: Quicksand;
}

body {
  background: #dbdbdb;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap;
  height: 100vh;
  width: 100vw;
  
}

html,
body {
  height: 100%;
}

html {
  background: #e0e0e0;
}


.counter {
  font-size: 50px;
}

.buttons button {
  font-size: 28px;
  width: 100px;
  margin: 0;
}




.label {
  display: block;
  margin-top: 10px;
  font-size: 20px;
}

.sprite {
  width: 150px;
  margin-top: 10px;
  margin-bottom: 10px;
}

.hr {
  margin-top: 20px;
  border: none;
  height: 1px;
  background-color: #ccc;
  width: 100%;
}



table {
  border-collapse: collapse;
  width: 100%;
}

th,
td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: center;
}

th {
  background-color: #f2f2f2;
  font-weight: bold;
}

tr:nth-child(even) {
  background-color: #f2f2f2;
}

tr:hover {
  background-color: #ddd;
}

.emoji {
  display: inline;
}

.list-card{
  margin-top: 25px;
}

.box {
  width: 20vw;
  height: 60vh;
  border-radius: 5px;
  box-shadow: 0 2px 30px rgba(black, 0.2);
  background: darken(#48cc3c, 20%);
  position: relative;
  overflow: hidden;
  transform: translate3d(0, 0, 0);
  min-width: 200px;
  min-height: 350px;
  background-color: #ccd043;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
  margin-top: 20px;
}

.wave {
  opacity: 0.3;
  position: absolute;
  top: 120%;
  left: 50%;
  background: white;
  width: 500px;
  height: 500px;
  margin-left: -250px;
  margin-top: -250px;
  transform-origin: 50% 48%;
  border-radius: 43%;
  animation: drift 3000ms infinite linear;
  z-index: 1;
}

.wave.-three {
  animation: drift 5000ms infinite linear;
  z-index: 2 !important;
  opacity: 0.2;
}

.wave.-two {
  animation: drift 7000ms infinite linear;
  opacity: 0.1;
  z-index: 3 !important;
}

.box:after {
  content: "";
  display: block;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  z-index: 11;
  transform: translate3d(0, 0, 0);
}

@keyframes drift {
  from {
    transform: rotate(0deg);
  }

  from {
    transform: rotate(360deg);
  }
}


.info {
  position: absolute;
  bottom: 0;
  width: 100%;
  /* height: 45%; */
  z-index: 4;
}

.location {
  text-align: center;
  font-weight: 800;
}

.date {
  text-align: center;
  margin-top: 5%;
  /* color: lighten(grey, 10%); */
  color: #000;
  font-size: 85%;
}

.temp {
  margin-top: 10%;
  text-align: center;
  
}

.weathercon {
  height: 55%;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 3em;
}

@media (max-width: 600px) {
  .box {
    width: 90vw;
    height: 80vh;
  }

  .wave {
    top: 85%;
  }

  .weathercon {
    font-size: 5em;
  }

  .info {
    font-size: 1.5rem;
  }
}

@media (max-height: 500px) {
  .box {
    height: 80vh;
  }

  .wave {
    top: 115%;
  }
}

body>span {
  width: 100vw;
  text-align: center;
  color: grey;
}
</style>
