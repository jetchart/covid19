<template>
  <div class="home container">
    <div class="row">
      <div class="col">
        <h2>TOTAL AT WORLD</h2>
      </div>
    </div>
    <div class="row">
      <div class="col bottom">
        <template v-if="!loading">
          <span class="total total-confirmed">{{totals.confirmed}}</span><br>
          <span class="description">TOTAL CONFIRMED</span>
        </template>
        <b-spinner v-else variant="warning" style="width: 3rem; height: 3rem;"></b-spinner>
      </div>
      <div class="col bottom">
        <template v-if="!loading">
          <span class="total total-death">{{totals.death}}</span><br>
          <span class="description">TOTAL DEATHS</span>
        </template>
        <b-spinner v-else variant="danger" style="width: 3rem; height: 3rem;"></b-spinner>
      </div>
      <div class="col bottom">
        <template v-if="!loading">
          <span class="total total-recovered">{{totals.recovered}}</span><br>
          <span class="description">TOTAL RECOVERED</span>
        </template>
        <b-spinner v-else variant="success" style="width: 3rem; height: 3rem;"></b-spinner>
      </div>
    </div>
    <hr>
      <template v-if="!loadingCountries">
        <search :countries="countries"></search>
      </template>
      <b-spinner v-else style="width: 3rem; height: 3rem;"></b-spinner>
  </div>
</template>

<script>
  import {mapGetters} from "vuex";
  import axios from 'axios'
  import Search from './Search'

  export default {
    name: 'Home',
    data () {
      return {
        loading: false,
        loadingCountries: true,
        countries: [],
        totals: {
          confirmed: 0,
          death: 0,
          recovered: 0,
        },
      }
    },
    computed: {
      ...mapGetters([ 'getConnected', 'getSocket'])
    },
    methods: {
      getSummary() {
        this.loading = true;
        axios.get("https://api.covid19api.com/summary")
          .then(response => {
            const data = response.data;
            this.totals.confirmed = data.Global.TotalConfirmed;
            this.totals.death = data.Global.TotalDeaths;
            this.totals.recovered = data.Global.TotalRecovered;
            this.loading = false;
          })
          .catch(error => {
            this.loading = false;
          })
      },
      getCountries() {
        this.loadingCountries = true;
        axios.get("https://api.covid19api.com/countries")
          .then(response => {
            const data = response.data;
            data.forEach(country => {
              var c = {};
              c.name = country.Country;
              c.slug = country.Slug;
              this.countries.push(c);
            });
            this.loadingCountries = false;
          })
          .catch(error => {
            this.loadingCountries = false;
          })
      },
    },
    mounted() {
      this.getSummary();
      this.getCountries();
    },
    watch: {
    },
    components: { Search }
  }
</script>

<style>

  .bottom {
    margin-bottom: 1rem;
  }

  .home {
    padding: 2rem;
  }

  .total {
    font-weight: 700;
    font-size: 50px;
  }

  .total-confirmed {
    color: yellow;
  }

  .total-death {
    color: red;
  }

  .total-recovered {
    color: green;
  }

  .description {
    font-weight: 700;
    font-size: 15px;
  }
</style>
