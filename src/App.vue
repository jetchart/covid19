<template>
  <div id="app">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <header-custom class="header-a header-t"></header-custom>
    <div class="content-body" v-bind:class="{'content': getConnected, 'bg-light': getConnected}">
      <router-view></router-view>
    </div>
    <footer-custom class="opacity-t"></footer-custom>
  </div>
</template>

<script>
  import 'bootstrap/dist/css/bootstrap.css'
  import 'bootstrap-vue/dist/bootstrap-vue.css'
  import HeaderCustom from './components/HeaderOrig'
  import FooterCustom from './components/Footer'
  import {mapGetters} from "vuex";

export default {
  name: 'app',
  computed: {
    ...mapGetters([ 'getConnected', ])
  },
  mounted() {
    window.scrollTo(0,0);
  },
  data () {
    return {
      msg: 'Welcome to Your Vue.js App'
    }
  },
  components: { HeaderCustom, FooterCustom }
}
</script>

<style>

#app {
  font-family: Lato,-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,"Helvetica Neue",Arial,"Noto Sans",sans-serif,"Apple Color Emoji","Segoe UI Emoji","Segoe UI Symbol","Noto Color Emoji";;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-color: white;
  height: 100vh;
  border-radius: 15px;
}

.content-body {
  padding-top: 4rem;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

.content {
  height: 65%;
  margin: 2rem;
  padding:20px;
  overflow-y: scroll;
  border-radius: 10px;
  -webkit-box-shadow: 0px 0px 8px 0px rgba(0,0,0,0.29);
  -moz-box-shadow: 0px 0px 8px 0px rgba(0,0,0,0.29);
  box-shadow: 0px 0px 8px 0px rgba(0,0,0,0.29);
}

.header-a {
  position: fixed;
}

.opacity-t {
  animation: opacity-animation 2s linear;
}

@keyframes opacity-animation {
  from { opacity: 0; }
  to { opacity: 1; }
}

.header-t {
   animation: header-animation 0.5s linear;
 }

@keyframes header-animation {
  from { top: -100px; }
  to { top: 0; }
}

</style>
