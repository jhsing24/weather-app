<template>
  <div id="app" :class="typeof weather.main == 'undefined' ? '' : 
  unit == 'metric' ? weather.main.temp > 16 ? 'warm':'cold' : weather.main.temp > 61 ? 'warm':'cold'">
    <main>
      <div class="header">
        <div class="search-box">
          <input 
            type="text" 
            class="search-bar" 
            placeholder="Search for a city or state and hit Enter!"
            v-model="query"
            @keypress="fetchWeather"
          />
        </div>
          <div class="switcher" v-bind:class="[ unit ]" @click="handler">
          <span class="unit-f">°F</span>
          <div class="switch"><span></span></div>
          <span class="unit-c">°C</span>
        </div>
      </div>
      
      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location">{{ weather.name }}, {{ weather.sys.country }}</div>
          <div class="date">{{dateBuilder()}}</div>
        </div>

          <div class="weather-box">
            <div class="temp">{{ Math.round(weather.main.temp) }}°{{this.tempUnit}}</div>
            <div class="weather">{{ weather.weather[0].main }}</div>
          </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      api_key: 'e2da82a7609d44976d9d210ba26dab1b',
      url_base: 'https://api.openweathermap.org/data/2.5/',
      query: '',
      weather: {},
      unit: 'imperial',
      tempUnit: 'F',
    }
  },
  methods: {
    fetchWeather(e) {
      if(e.key == 'Enter') {
        fetch(`${this.url_base}weather?q=${this.query}&units=${this.unit}&appid=${this.api_key}`)
          .then(res => {
            return res.json();
          }).then(this.setResults);
      }
    },
    fetchWeatherSwitcher() {
      fetch(`${this.url_base}weather?q=${this.query}&units=${this.unit}&appid=${this.api_key}`)
        .then(res => {
          return res.json();
        }).then(this.setResults);
    },
    setResults (results) {
      this.weather = results;
    },
    dateBuilder () {
      let d = new Date();
      let months = ["Jan.", "Feb.", "Mar.", "Apr.", "May", "Jun.", "Jul.", "Aug.", "Sep.", "Oct.", "Nov.", "Dec."]
      let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day} ${date} ${month} ${year}`;
    },
    changeUnit() {
        if (this.unit === "metric") {
            this.unit = "imperial";
            this.tempUnit = "F";
        } else {
            this.unit = "metric";
            this.tempUnit = "C";
        }
    },
    handler() {
      this.changeUnit()
      this.fetchWeatherSwitcher();
      
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Jost:ital,wght@0,100;0,200;0,300;0,400;0,500;1,100;1,200;1,300;1,400&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'jost', sans-serif;
}

#app {
  background-image: url('./assets/basebg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s ease-in-out;
}

#app.warm {
  background-image: url('./assets/warmbg.jpg');
}

#app.cold {
  background-image: url('./assets/coldbg.jpg');
}

main {
  height: 100vh;
  padding: 25px;
  background-image: linear-gradient(to bottom, rgba(0,0,0,0.25), rgba(0,0,0,0.75));
}
.header {
  display: flex;
  flex-direction: row;
  align-items: center;
  flex: flex;
  justify-content: space-between;
}
.search-box {
  width: 80%;
}

.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;
  color: rgba(205, 205, 205, 0.25);
  font-size: 20px;
  
  appearance: none;
  border: none;
  outline: none;
  background: none;

  background-color: rgba(255, 255, 255, 0.15);
  
  box-shadow: 0 0 8px rgba(0, 0, 0, 0.25);
  border-radius: 0px 15px;
  transition: 0.4s;
}
@media (max-width: 450px) {
  .search-box .search-bar {
    font-size: 15px;
    width: 270px;
  }
}

.search-box .search-bar:focus {
  background-color: rgba(255, 255, 255, 0.75);
  box-shadow: 0 0 16px rgba(0, 0, 0, 0.25);
  color: rgb(49, 49, 49);
  border-radius: 15px 0px;
}

.location-box .location {
  color: white;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 2px rgba(0, 0, 0, 0.25)
}
.location-box .date {
  color: white;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}
.weather-wrap {
  padding: 20px;
}
.weather-box {
  text-align: center;
}

.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: white;
  font-size: 100px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 15px;

  margin: 30px 0px;
  box-shadow: 4px 4px rgba(0, 0, 0, 0.25);
}

.weather-box .weather {
  font-size: 50px;
  font-weight: 700;
  color: white;
  font-style: italic;
}

.switcher {
  display: flex;
  height: 30px;
  width: 85px;
  cursor: pointer;
  float: right;
  padding: 5px;
  background-color: rgba(255, 255, 255, 0.15);
  border-radius: 20px;
  top: 0;
}
.switcher span {
     display: block;
     width: 16px;
     line-height: 20px;
     text-align: center;
     color: #ccc;
     transition: color 0.15s linear;
}
.switcher span.unit-f {
     float: left;
}
.switcher span.unit-c {
     float: right;
}
.switcher.imperial .unit-c {
     color: #0d090a;
}

.switcher.imperial .switch span {
     margin: 0 20px 0 0;
}
.switcher.metric .unit-f {
     color: #0d090a;
}
.switcher.metric .switch span {
     margin: 0 0 0 20px;
}
.switch {
     width: 40px;
     height: 20px;
     border-radius: 10px;
     border: 2px solid #0d090a;
     padding: 2px;
     float:inherit;
     box-sizing: border-box;
}
.switch span {
     display: block;
     width: 12px;
     height: 12px;
     border-radius: 6px;
     background-color: #0d090a;
     transition: margin 0.15s ease-out;
}
</style>
