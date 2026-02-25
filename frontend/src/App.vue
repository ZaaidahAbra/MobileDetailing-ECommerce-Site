<template>
  <AppNavbar />
  <div id="app-content">
    <router-view/>
  </div>
  <AppFooter />
  <SpeedInsights />
</template>

<script>
import AppNavbar from './components/AppNavbar.vue';
import AppFooter from './components/AppFooter.vue';
import { SpeedInsights } from '@vercel/speed-insights/vue';
export default {
  components: { AppNavbar, AppFooter, SpeedInsights }
}
</script>

<style>
#app {
  font-family: "Playfair Display", serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#app-content {
  margin-top: 90px; 
}

@media (max-width: 768px) {
  #app-content {
    margin-top: 140px;
  }
}
</style>
