<template>
  <div>
    <HomeComponent />
  </div>
</template>

<script>
import HomeComponent from './components/HomeComponent.vue';

export default {
  name: 'App',
  components: {
    HomeComponent
  }
}
</script>

<style>
#app {
  font-family: "Inter", "Roboto";
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
}
</style>
