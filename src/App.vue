<template>
  <div id="app">
    <QuizGame/>
  </div>
</template>

<script>
import QuizGame from "./components/QuizGame";

export default {
  name: "App",
  components: {
    QuizGame
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 1em;
}
</style>
