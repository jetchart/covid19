<template>
  <div>
    <div class="row">
      <div class="col">
        <h5>TOTAL BY COUNTRY</h5>
      </div>
    </div>
    <div class="row">
      <div class="col bottom" align="center">
        <div class="col-md-5">
          <b-form-select v-model="countrySelected" @change="showData()" value-field="slug" text-field="name" :options="countries"></b-form-select>
        </div>
      </div>
    </div>
    <div class="row" v-if="countrySelected">
      <div class="col bottom">
        <template v-if="!loadingConfirmed">
          <span class="total total-confirmed">{{country.confirmed.count}}</span><br>
          <span class="description">CONFIRMED</span>
        </template>
        <b-spinner v-else variant="warning"></b-spinner>
      </div>
      <div class="col bottom">
        <template v-if="!loadingDeath">
          <span class="total total-death">{{country.death.count}}</span><br>
          <span class="description">DEATHS</span>
        </template>
        <b-spinner v-else variant="danger"></b-spinner>
      </div>
      <div class="col bottom">
        <template v-if="!loadingRecovered">
          <span class="total total-recovered">{{country.recovered.count}}</span><br>
          <span class="description">RECOVERED</span>
        </template>
        <b-spinner v-else variant="success"></b-spinner>
      </div>
    </div>
  </div>
</template>

<script>

  import axios from "axios";

  export default {
    name: 'Search',
    props: ['countries'],
    components: { },
    data () {
      return {
        loadingConfirmed: false,
        loadingDeath: false,
        loadingRecovered: false,
        countrySelected: null,
        country: {
          confirmed: {
            count: 0,
            date: null
          },
          death: {
            count: 0,
            date: null
          },
          recovered: {
            count: 0,
            date: null
          }
        },
      }
    },
    computed: {
    },
    mounted() {
    },
    methods: {
      showData() {
        if (!this.countrySelected)
          return;
        this.getConfirmed();
        this.getDeath();
        this.getRecovered();
      },
      getConfirmed() {
        this.loadingConfirmed = true;
        axios.get(`https://api.covid19api.com/country/${this.countrySelected}/status/confirmed`)
          .then(response => {
            const data = response.data[response.data.length - 1];
            this.country.confirmed.count = data.Cases;
            this.country.confirmed.date = data.Date;
            this.loadingConfirmed = false;
          })
          .catch(error => {
            this.loadingConfirmed = false;
          })
      },
      getDeath() {
        this.loadingDeath = true;
        axios.get(`https://api.covid19api.com/country/${this.countrySelected}/status/deaths`)
          .then(response => {
            const data = response.data[response.data.length - 1];
            this.country.death.count = data.Cases;
            this.country.death.date = data.Date;
            this.loadingDeath = false;
          })
          .catch(error => {
            this.loadingDeath = false;
          })
      },
      getRecovered() {
        this.loadingRecovered = true;
        axios.get(`https://api.covid19api.com/country/${this.countrySelected}/status/recovered`)
          .then(response => {
            const data = response.data[response.data.length - 1];
            this.country.recovered.count = data.Cases;
            this.country.recovered.date = data.Date;
            this.loadingRecovered = false;
          })
          .catch(error => {
            this.loadingRecovered = false;
          })
      },
    },
    watch: {
    },
  }
</script>

<style>

  .total {
    font-weight: 500;
    font-size: 30px;
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
    font-weight: 500;
    font-size: 10px;
  }

  .bottom {
    margin-bottom: 1rem;
  }

</style>
