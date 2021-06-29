<template>
  <div class="test">
    <h1>{{ msg }}</h1>
    <field label="Department">
      <control>
        <div class="select" style="display: none;">
          <select name="s" v-model="selected" @change="updateData">
            <option value="">Select symbol</option>
            <option value="AAIC">AAIC</option>
            <option value="IBM">IBM</option>
            <option value="AAL">AAL</option>
            <option value="AAMC">AAMC</option>
            <option value="AAN">AAN</option>
          </select>
        </div>
      </control>
    </field>
    <div id="chart">
      <apexchart type="candlestick" height="350" :options="chartOptions" :series="series"></apexchart>
    </div>
  </div>
</template>

<script>
import VueApexCharts from 'vue3-apexcharts'
import axios from 'axios'
export default {
  name: 'CandleStick',
  props: {
    msg: String
  },
  components: {
    apexchart: VueApexCharts
  },
  data () {
    return {
      selected: '',
      results: [],
      metadata: 'Meta Data',
      series: [{
        data: []
      }],
      chartOptions: {
        chart: {
          type: 'candlestick',
          height: 350
        },
        title: {
          text: 'CandleStick Symbol Chart',
          align: 'left'
        },
        xaxis: {
          type: 'datetime'
        },
        yaxis: {
          tooltip: {
            enabled: true
          }
        }
      }
    }
  },
  mounted () {
    axios.get('https://www.alphavantage.co/query?function=TIME_SERIES_DAILY&symbol=IBM&apikey=demo')
      .then(response => {
        const data = response.data.['Time Series (Daily)']
        let temp
        const testarray = []
        for (const d in data) {
          temp = { x: new Date(d), y: [data[d]['1. open'], data[d]['2. high'], data[d]['3. low'], data[d]['4. close']] }
          testarray.push(temp)
        }
        this.series[0].data = testarray
      })
      .catch(error => {
        console.log(error)
      })
  },
  methods: {
    formatData (result) {
      result.forEach(fields => {
        this.series.data.push(fields)
      })
    },
    updateData () {
      if (this.selected === '') {
        this.selected = 'IBM'
      }
      axios.get('https://www.alphavantage.co/query?function=TIME_SERIES_DAILY&symbol=' + this.selected + '&apikey=demo')
        .then(response => {
          const data = response.data.['Time Series (Daily)']
          let temp
          const testarray = []
          for (const d in data) {
            temp = { x: new Date(d), y: [data[d]['1. open'], data[d]['2. high'], data[d]['3. low'], data[d]['4. close']] }
            testarray.push(temp)
          }
          this.series[0].data = testarray
        })
        .catch(error => {
          console.log(error)
        })
    }
  }
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
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
</style>
