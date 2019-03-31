<template>
  <div id="app" class="app">
    <h1>Look Up US Weather</h1>
    <WeatherLookup/>
  </div>
</template>

<script>
import WeatherLookup from "./components/Weather.vue";

export default {
  name: "app",
  components: {
    WeatherLookup
  }
};
</script>

<style>
.app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  font-size: 16px;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  max-width: 500px;
  margin: 0 auto;
}

.list-unstyled {
  padding: 0;
  list-style: none;
}
.list-unstyled > li + li {
  margin-top: 1em;
}

.list-striped > li:nth-child(odd) {
  background-color: #eee;
}

.list-padded > li {
  padding: 10px;
}

.list-inline {
  display: flex;
  flex-wrap: nowrap;
  padding: 0;
  list-style: none;
}

.list-inline > li {
  display: block;
  flex: 1 1 10%;
}

.text-bold {
  font-weight: bold;
}

.text-left {
  text-align: left;
}

.text-red {
  color: red;
}
.text-blue {
  color: blue;
}
</style>
