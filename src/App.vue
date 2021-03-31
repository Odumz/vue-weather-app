<template>
  <div id="app" :class="typeof weather.main != 'undefined' ? weather.weather[0].main : ''">
    <main>
      <div class="search-box">
        <input 
        type="text" 
        class="search-bar" 
        placeholder="eg Florida, USA..."
        v-model="query" 
        @keypress="fetchWeather"
        />
      </div>
      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location">{{ weather.name }}</div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>

        <div class="weather-box">
          <div class="temp">
            {{ Math.round(weather.main.temp)}} C
            <img :src=source />
          </div>
          <div class="weather">
            {{ weather.weather[0].main }}
          </div>
          <div class="weather_description">
            {{ weather.weather[0].description }}
          </div>
          <div class="temp_minmax">
            Max: {{ Math.round(weather.main.temp_max )}} C || Min: {{ Math.floor(weather.main.temp_min )}} C 
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
import swal from 'sweetalert2';

export default {
  name: 'App',
  data () {
    return {
      query: '',
      weather: {},
      source: ''
    }
  },
  methods: {
    fetchWeather (e) {
      if (e.key == "Enter") {
        fetch (`${process.env.VUE_APP_URLBASE}weather?q=${this.query}&units=metric&APPID=${process.env.VUE_APP_APIKEY}`)
        .then (res => {
          if (res.status == '404') {
            swal.fire('Oops...', 'Looks like we don\'t have that data', 'info');
            return;
          } else {
            return res.json();
          }
        }).then(this.setResults).then(this.setSource)
        .catch((error) => {
          swal.fire(error.message, 'error');
        });
      }
    },
    setResults (results) {
      this.weather = results;
    },
    setSource () {
      this.source = "http://openweathermap.org/img/wn/" + this.weather.weather[0].icon + ".png";
    },
    dateBuilder () {
      let d = new Date();
      let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
      let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day} ${date} ${month} ${year}`;
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'montserrat', sans-serif;
}

#app {
  background-image: url('https://res.cloudinary.com/griffintech/image/upload/v1615498840/cold_bg_dlvbpz.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

#app.Clear {
  background-image: url('https://res.cloudinary.com/griffintech/image/upload/v1615498846/warm_bg_yedlw9.jpg');
}

main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.25), rgba(0, 0, 0, 0.75));
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;
  color: #313131;
  font-size: 20px;
  appearance: none;
  border: none;
  outline: none;
  background: none;
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
}

.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px 16px 0px;
}

.location-box .location {
  color: #fff;
  font-size: 34px;
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
}

.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #fff;
  font-size: 102px;
  font-weight: 900;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.temp_minmax {
  display: inline-block;
  padding: 10px 25px;
  color: #fff;
  font-size: 12px;
  font-weight: 900;
  text-shadow: 3px 4px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .weather {
  color: #fff;
  font-size: 48px;
  font-weight: 700;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather_description {
  color: #fff;
  font-size: 18px;
  font-weight: 400;
  font-style: italic;
  text-shadow: 3px 4px rgba(0, 0, 0, 0.25);
}

@media only screen and (max-width: 420px) {
  .weather-box .temp {
    font-size: 82px;
  }
}

</style>
