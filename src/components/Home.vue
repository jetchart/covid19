<template>
  <div class="home container">
    <b-alert :show="dismissCountDown" dismissible variant="danger" @dismissed="dismissCountDown=0" @dismiss-count-down="countDownChanged">
      ¡Ups! An error occurred while retrieving the information, please try again later
    </b-alert>
    <div class="row">
      <div class="col">
        <h2>TOTAL WORLD</h2>
      </div>
    </div>
    <div class="row">
      <div class="col bottom">
        <template v-if="!loading">
          <span class="total total-confirmed">{{totals.confirmed}}</span><br>
          <span class="description">CONFIRMED</span><br>
          <span class="date">({{totals.date}})</span>
        </template>
        <b-spinner v-else variant="warning" style="width: 3rem; height: 3rem;"></b-spinner>
      </div>
      <div class="col bottom">
        <template v-if="!loading">
          <span class="total total-death">{{totals.death}}</span><br>
          <span class="description">DEATHS</span><br>
          <span class="date">({{totals.date}})</span>
        </template>
        <b-spinner v-else variant="danger" style="width: 3rem; height: 3rem;"></b-spinner>
      </div>
      <div class="col bottom">
        <template v-if="!loading">
          <span class="total total-recovered">{{totals.recovered}}</span><br>
          <span class="description">RECOVERED</span><br>
          <span class="date">({{totals.date}})</span>
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
        dismissSecs: 5,
        dismissCountDown: 0,
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
            this.totals.date = new Date(data.Date).toLocaleString();
            this.loading = false;
          })
          .catch(error => {
            this.showAlert();
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
            this.sort();
            this.loadingCountries = false;
          })
          .catch(error => {
            this.showAlert();
            this.loadingCountries = false;
          })
      },
      sort() {
        this.countries.sort(function (a, b) {
          if (a.name > b.name)
            return 1;
          if (a.name < b.name)
            return -1;
          return 0;
        });
      },
      formatDate(date) {
        return date.substring(8,10) + "/" + date.substring(5,7) + "/" + date.substring(0,4);
      },
      countDownChanged(dismissCountDown) {
        this.dismissCountDown = dismissCountDown
      },
      showAlert() {
        this.dismissCountDown = this.dismissSecs
      }
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

<style scoped>

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
    color: #ffcf40;
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

  h2 {
    font-weight: 700;
  }

  .date {
    font-weight: 400;
    font-size: 9px;
  }

</style>
