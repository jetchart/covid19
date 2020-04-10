<template>
  <div class="header">
    <a href="https://portfolio-jme.herokuapp.com/" target="_blank"><img align="left" alt="" src="../assets/www5.png" height="30px" width="30px"></a>
    <span class="header-title">COVID19 STATS</span>
    <div class="brand" align="right">
      <img class="brand-image" src="../assets/coronavirus.png" height="60px" width="60px">
    </div>
  </div>
</template>

<script>

  import {mapGetters} from "vuex";

  export default {
    name: 'Header',
    data () {
      return {
        user: null,
      }
    },
    methods: {
      quit() {
        this.unsubscribe();
        this.$router.push("/");
      },
      unsubscribe() {
        this.getSocket.emit('unsubscribe');
        this.$store.commit("unjoin");
      },
    },
    computed: {
      ...mapGetters([ 'getUser', 'getConnected', 'getSocket'])
    },
    mounted(){
    },
  }
</script>

<style>

  .header-title {
    font-weight: 900;
    font-size: 20px;
  }

  .header {
    z-index: 999;
    height: 4rem;
    width: 100%;
    padding: 20px;
    text-align: center;
    background: #271979;
    color: white;
    //text-transform: uppercase;
    font-size: 1rem;
    font-family: Montserrat,-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,"Helvetica Neue",Arial,"Noto Sans",sans-serif,"Apple Color Emoji","Segoe UI Emoji","Segoe UI Symbol","Noto Color Emoji";
    font-weight: 700;
    -webkit-box-shadow: 0px -12px 14px 10px rgba(0,0,0,0.75);
    -moz-box-shadow: 0px -12px 14px 10px rgba(0,0,0,0.75);
    box-shadow: 0px -12px 14px 10px rgba(0,0,0,0.75);
  }

  .brand {
    position: relative;
    top: -20px;
    margin-right: 45px;
    left: 50px;
  }

  .www {
    cursor: pointer;
  }

  .brand-image {
    animation-name: brand-animation;
    animation: brand-animation 5s linear infinite;
  }

  @keyframes brand-animation {
    to{ transform: rotate(360deg); }
  }

</style>
