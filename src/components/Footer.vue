<template>
  <div class="footer">
    <div class="row header-row">
      <div class="col" align="right">
        <span class="footer-desc">by JME</span>
      </div>
    </div>
  </div>
</template>

<script>

  import {mapGetters} from "vuex";

  export default {
    name: 'Footer',
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

  .footer {
    height: 1.8rem;
    width: 100%;
    position: fixed;
    bottom: 0;
    padding-right: 0.2rem;
    text-align: center;
    color: black;
    //text-transform: uppercase;
    font-size: 1rem;
    font-family: Montserrat,-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,"Helvetica Neue",Arial,"Noto Sans",sans-serif,"Apple Color Emoji","Segoe UI Emoji","Segoe UI Symbol","Noto Color Emoji";
    font-weight: 700;
  }

  .header-row {
    align-items: center;
    vertical-align: middle;
  }

  .footer-desc {
    font-size: 10px;
    font-weight: 500;
  }

</style>
