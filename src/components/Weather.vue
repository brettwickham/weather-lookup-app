<template>
  <div class="current">
    <form @submit.prevent="handleSubmit()">
      <h3>Enter a zip code below:</h3>
      <div class="fieldset">
        <label for="zip">Zip Code</label>
        <input v-model="zip" name="zip" placeholder="Zip Code">
        <button type="submit">Get Weather</button>
      </div>
    </form>
    <div v-if="current.main">
      <h3>{{current.name}}</h3>
      <ul class="list-unstyled">
        <li class="text-bold">Currently</li>
        <ul class="list-inline list-padded">
          <li>{{current.weather[0].main}}</li>
          <li>{{current.main.temp | round}}&deg;</li>
          <li>
            <span class="text-red">{{forecast24LowHigh.high | round }}&deg;</span> /
            <span class="text-blue">{{forecast24LowHigh.low | round }}&deg;</span>
          </li>
        </ul>
      </ul>
    </div>
    <div class="text-left" v-if="forecast.list">
      <h3>24-Hour Forecast</h3>
      <ul class="list-unstyled list-striped list-padded">
        <li v-for="day in forecast24" :key="day.dt">
          <span class="text-bold">{{day.dt_txt | formatDate}}</span>
          <ul class="list-inline">
            <li>
              {{day.weather[0].main}}
              <br>
              {{day.main.temp | round}}&deg;
            </li>
            <li>
              Wind
              <br>
              {{day.wind.speed | round}} mph
            </li>
            <li>
              Humidity
              <br>
              {{day.main.humidity}}%
            </li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import moment from "moment";

export default {
  name: "WeatherLookup",
  data() {
    return {
      zip: this.getZip(),
      current: {},
      forecast: {},
      forecast24: [],
      forecast24LowHigh: {},
      errors: []
    };
  },

  filters: {
    round: function(value) {
      if (isNaN(value)) return "";
      return Math.round(value);
    },
    formatDate: function(value) {
      if (!isNaN(value)) return "";
      return moment
        .utc(value)
        .local()
        .format("h a");
    }
  },

  methods: {
    getData(type) {
      let url = "https://api.openweathermap.org/data/2.5/";

      switch (type) {
        case "current":
          url += "weather";
          break;
        case "forecast":
          url += "forecast";
          break;
      }
      url += `?zip=${
        this.zip
      },us&units=imperial&appid=03f2d0ded03aedb03c7ef0c4fb423cc4`;

      const fetchData = async () => {
        try {
          const response = await axios.get(url);
          this[type] = response.data;
          if (type === "forecast") {
            this[`${type}24`] = this.getForecast24();
            this[`${type}24LowHigh`] = this.getForecast24LowHigh();
          }
        } catch (error) {
          this.errors.push(error);
        }
      };

      fetchData();
    },

    saveZip() {
      window.localStorage.setItem("zip", this.zip);
    },

    getZip() {
      return window.localStorage.getItem("zip") || "64114";
    },

    getForecast24() {
      return this.forecast.list.slice(0, 8);
    },

    getForecast24LowHigh() {
      const min = this.forecast24.reduce((a, b) => {
        return b.main.temp < a.main.temp ? b : a;
      });
      const max = this.forecast24.reduce((a, b) => {
        return b.main.temp > a.main.temp ? b : a;
      });

      return {
        low: min.main.temp,
        high: max.main.temp
      };
    },

    handleSubmit() {
      if (this.zip) {
        this.saveZip();
        this.getData("current");
        this.getData("forecast");
      }
    }
  },

  mounted() {
    this.handleSubmit();
  }
};
</script>

<style scoped>
.fieldset {
  padding: 0;
  margin: 0;
  border: 0;
  display: flex;
  flex-flow: row;
  align-items: center;
  justify-content: center;
}

label,
input,
button {
  font-size: 16px !important;
  line-height: 1.2;
  font-size: inherit;
  padding: 0.4em;
  margin: 0;
  box-sizing: border-box;
}

input,
button {
  min-height: 36px;
}

input {
  border: 1px solid #2c3e50;
  border-radius: 0;
  min-width: 150px;
}

button {
  min-width: 150px;
  background-color: #2c3e50;
  color: #fff;
  font-weight: bold;
  border: 2px solid #2c3e50;
  padding-left: 1em;
  padding-right: 1em;
  border-radius: 0;
  opacity: 1;
  transition: 0.2s opacity ease-in-out;
}

button:hover,
button:focus,
button:active {
  background-color: #2c3e50;
  color: #fff;
  border-color: #2c3e50;
  opacity: 0.8;
  outline: none;
}
</style>
