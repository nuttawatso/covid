<template>
  <div>
    <h1>COVID-19</h1>
    <div v-if="loading">Loading...</div>
    <div v-else>
      <div>
      <label for="date">Select date:</label>  
      <input type="date" id="date" v-model="showDate" @change="updateData" />
    </div>
    <ChartCountry :data="data" :selectedDate="selectedDate"/>
    <div class="table-container">
      <b-table>
        <thead>
           <tr>
            <th>Country</th>
            <th>Cases</th>
            <th>Deaths</th>
            <th>Recovered</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in data" :key="item.country">
            <td>{{ item.country }}</td>
            <td>{{ item.timeline.cases[selectedDate]}}</td>
            <td>{{ item.timeline.deaths[selectedDate]}}</td>
            <td>{{ item.timeline.recovered[selectedDate]}}</td>
          </tr>
        </tbody>
      </b-table>
    </div>
    </div>

  </div>

</template>

<script>
import moment from 'moment';
import axios from 'axios';
import ChartCountry from './ChartCountry.vue';
export default {
    name:"DataDisplay",
    components:{
      ChartCountry
    },
    data() {
      return {
        data:[],
        loading: true,
        selectedDate:"",

      }
    },
    mounted() {
        this.getData();
        this.showDate = moment(this.selectedDate, 'M/D/YY').format();
    },
    methods: {
        getData() {
            axios.get('https://disease.sh/v3/covid-19/historical?lastdays=31')
                .then(response => {
                    this.data = response.data;
                    this.selectedDate = Object.keys(this.data[0].timeline.cases)[Object.keys(this.data[0].timeline.cases).length - 1];
                    this.loading = false;
                })
                .catch(error => {
                    console.log(error);
                });
        },
        updateData() {
            this.selectedDate = moment(this.showDate).format('M/D/YY');
        }
    }
}

</script>

<style>

@media screen and (max-width: 767px) {

/* adjust table styles for smaller screens */
.table-responsive {
    overflow-x: auto;
}

.styled-table {
    font-size: 14px;
}

.styled-table th,
.styled-table td {
    padding: 8px;
}
}
</style>