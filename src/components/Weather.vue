<template>
  <div class="current">
    <form @submit.prevent="handleSubmit()">
      <h3>Enter a zip code below:</h3>
      <input v-model="zip" placeholder="Zip Code">
      <button>Get Weather</button>
    </form>
    <div v-if="current.main">
      <h3>{{current.name}}</h3>
      <ul class="list-unstyled">
        <li>Currently</li>
        <ul class="list-inline list-padded">
          <li>{{current.weather[0].main}}</li>
          <li>{{current.main.temp | round}}&deg;</li>
          <li>
            <span class="text-red">{{current.main.temp_max | round }}&deg;</span> /
            <span class="text-blue">{{current.main.temp_min | round }}&deg;</span>
          </li>
        </ul>
      </ul>
    </div>
    <div class="text-left" v-if="forecast.list">
      <h3>Short-Range Forecast</h3>
      <ul class="list-unstyled list-striped list-padded">
        <li v-for="day in forecast.list.slice(0, 10)" :key="day.dt">
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
        .format("MMM D h a");
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
