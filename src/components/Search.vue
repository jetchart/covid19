<template>
  <div>
    <b-alert :show="dismissCountDown" dismissible variant="danger" @dismissed="dismissCountDown=0" @dismiss-count-down="countDownChanged">
      ¡Ups! An error occurred while retrieving the information, please try again later
    </b-alert>
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
          <b-tooltip ref="tooltipConfirmed" target="country-confirmed">{{country.confirmed.count - country.confirmed.countBefore}} more than {{country.confirmed.dateBefore}}</b-tooltip>
          <div id="country-confirmed" @click="$refs.tooltipConfirmed.$emit('show')">
            <span class="total total-confirmed">
              {{country.confirmed.count}}<b-icon class="elevation" font-scale="0.5" icon="exclamation-circle-fill" variant="warning"></b-icon>
            </span>
          </div>
          <span class="description">CONFIRMED</span><br>
          <span class="date">({{country.confirmed.date}})</span>
        </template>
        <b-spinner v-else variant="warning"></b-spinner>
      </div>
      <div class="col bottom">
        <template v-if="!loadingDeath">
          <b-tooltip ref="tooltipDeath" target="country-death">{{country.death.count - country.death.countBefore}} more than {{country.death.dateBefore}}</b-tooltip>
          <div id="country-death" @click="$refs.tooltipDeath.$emit('show')">
            <span class="total total-death">
              {{country.death.count}}<b-icon class="elevation" font-scale="0.5" icon="exclamation-circle-fill" variant="danger"></b-icon>
            </span>
          </div>
          <span class="description">DEATHS</span><br>
          <span class="date">({{country.death.date}})</span>
        </template>
        <b-spinner v-else variant="danger"></b-spinner>
      </div>
      <div class="col bottom">
        <template v-if="!loadingRecovered">
          <b-tooltip ref="tooltipRecovered" target="country-recovered">{{country.recovered.count - country.recovered.countBefore}} more than {{country.recovered.dateBefore}}</b-tooltip>
          <div id="country-recovered" @click="$refs.tooltipRecovered.$emit('show')">
            <span class="total total-recovered">
              {{country.recovered.count}}<b-icon class="elevation" font-scale="0.5" icon="exclamation-circle-fill" variant="success"></b-icon>
            </span>
          </div>
          <span class="description">RECOVERED</span><br>
          <span class="date">({{country.recovered.date}})</span>
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
        dismissSecs: 5,
        dismissCountDown: 0,
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
        axios.get(`https://api.covid19api.com/total/dayone/country/${this.countrySelected}/status/confirmed`)
          .then(response => {
            const data = response.data[response.data.length - 1];
            this.country.confirmed.count = data.Cases;
            this.country.confirmed.date = new Date(data.Date).toLocaleString();
            this.getDiferenceConfirmed(response.data);
            this.loadingConfirmed = false;
          })
          .catch(error => {
            this.showAlert();
            this.loadingConfirmed = false;
          })
      },
      getDeath() {
        this.loadingDeath = true;
        axios.get(`https://api.covid19api.com/total/dayone/country/${this.countrySelected}/status/deaths`)
          .then(response => {
            const data = response.data[response.data.length - 1];
            this.country.death.count = data.Cases;
            this.country.death.date = new Date(data.Date).toLocaleString();
            this.getDiferenceDeaths(response.data);
            this.loadingDeath = false;
          })
          .catch(error => {
            this.showAlert();
            this.loadingDeath = false;
          })
      },
      getRecovered() {
        this.loadingRecovered = true;
        axios.get(`https://api.covid19api.com/total/dayone/country/${this.countrySelected}/status/recovered`)
          .then(response => {
            const data = response.data[response.data.length - 1];
            this.country.recovered.count = data.Cases;
            this.country.recovered.date = new Date(data.Date).toLocaleString();
            this.getDiferenceRecovered(response.data);
            this.loadingRecovered = false;
          })
          .catch(error => {
            this.showAlert();
            this.loadingRecovered = false;
          })
      },
      getDiferenceConfirmed(data) {
        let before = null;
        if (data.length > 1)
          before = data[data.length - 2];
        else
          before = data[data.length - 1];
        this.country.confirmed.countBefore = before.Cases;
        this.country.confirmed.dateBefore = this.formatDate(before.Date);
      },
      getDiferenceDeaths(data) {
        let before = null;
        if (data.length > 1)
          before = data[data.length - 2];
        else
          before = data[data.length - 1];
        this.country.death.countBefore = before.Cases;
        this.country.death.dateBefore = this.formatDate(before.Date);
      },
      getDiferenceRecovered(data) {
        let before = null;
        if (data.length > 1)
          before = data[data.length - 2];
        else
          before = data[data.length - 1];
        this.country.recovered.countBefore = before.Cases;
        this.country.recovered.dateBefore = this.formatDate(before.Date);
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
    watch: {
    },
  }
</script>

<style>

  .total {
    font-weight: 700;
    font-size: 25px;
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
    font-size: 10px;
  }

  .bottom {
    margin-bottom: 1rem;
  }

  .date {
    font-weight: 400;
    font-size: 9px;
  }

  h5 {
    font-weight: 700;
  }

  .elevation {
    margin-bottom: 0.8rem;
    margin-left: 0.2rem;
  }

</style>
