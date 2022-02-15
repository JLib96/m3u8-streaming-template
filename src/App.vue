<template>
  <div id="app">
    <HlsVideoComponent />
  </div>
</template>

<script>
import HlsVideoComponent from "./components/HlsVideoComponent";

export default {
  name: "App",
  components: {
    HlsVideoComponent,
  },
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
