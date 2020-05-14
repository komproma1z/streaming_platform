<template>
  <v-app>
    <div id="app">
      <Container />
    </div>
  </v-app>
</template>

<script>
import Container from "./components/Container";
export default {
  name: "app",
  components: {
    Container
  },
   mounted: function() {
    let elHtml = document.getElementsByTagName('html')[0]
    elHtml.style.overflowY = 'auto'
  },
  destroyed: function() {
    let elHtml = document.getElementsByTagName('html')[0]
    elHtml.style.overflowY = null
  }
};
</script>

<style>
  * {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
    font-family: Roboto;
    overflow: hidden;
  }
  #app {
    width: 100vw;
    height: 100vh;
  }
</style>