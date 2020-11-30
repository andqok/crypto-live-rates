<template>
  <div class="home">
    <div class="crypto-list">
      <crypto-item
          v-bind:key="index"
          v-for="(crypto, index) of currencies"
          v-bind:name="crypto"
          v-bind:data="lastVal[ crypto['CoinInfo']['Internal'] ]"
          v-bind:isBase="crypto['CoinInfo']['Internal'] === base"
          v-on:set-base="(base) => setBase(base)"
      ></crypto-item>
    </div>
    <div class="crypto-chart">
      <span>{{`USD for 1 ${base}, last ${lastMin} minute${lastMin === 1 ? '' : 's'}`}}</span>
      <apexchart type="area" height="350" :options="chartOptions" :series="series"></apexchart>
    </div>
  </div>
</template>

<script>
import CryptoItem from '@/components/CryptoItem.vue'
import {Data as currencies} from './coins.json'
const apiKey = '22f63deb9b34edc873c38702aab830bbc0f932c44eb639bad5b801dbbfb4469a'
const wsUrl = `wss://streamer.cryptocompare.com/v2&api_key=${apiKey}`
import ApexCharts from 'vue3-apexcharts'
window.finCache = {}

export default {
  name: 'Home',
  components: {
    CryptoItem,
    'apexchart': ApexCharts,
  },
  data() {
    return {
      currencies,
      base: 'BTC',
      lastMin: 2,
      series: [{
        name: this.base,
        data: [],
      }],
      lastVal: {},
      chartOptions: {
        chart: {
          type: 'area',
          stacked: false,
          height: 450,
          zoom: {
            type: 'x',
            enabled: true,
            autoScaleYaxis: true
          },
          toolbar: {
            autoSelected: 'zoom'
          }
        },
        dataLabels: {
          enabled: false
        },
        markers: {
          size: 0,
        },
        title: {
          text: ``,
          align: 'left'
        },
        fill: {
          type: 'gradient',
          gradient: {
            shadeIntensity: 1,
            inverseColors: false,
            opacityFrom: 0.5,
            opacityTo: 0,
            stops: [0, 90, 100]
          },
        },
        yaxis: {
          title: {
            text: 'Price'
          },
        },
        xaxis: {
          type: 'time',
        },
        tooltip: {
          shared: false,
        }
      },
    }
  },
  created() {
    this.ws = new WebSocket(wsUrl)
    this.ws.addEventListener('open', () => {
      for (let {CoinInfo} of currencies) {
        if (!window.finCache[CoinInfo.Name]) {
          window.finCache[CoinInfo.Name] = []
        }
        const str = JSON.stringify({
          "action": "SubAdd",
          "subs": [`0~Coinbase~${CoinInfo.Name}~USD`],
        }, )
        this.ws.send(str)
      }
    })
    this.ws.addEventListener('message',  (event) => {
      try {
        const msg = JSON.parse(event.data)
        if (msg.TS && msg.P) {
          this.lastVal[msg.FSYM] = msg.P;
          window.finCache[msg.FSYM].push(msg);
          if (msg.FSYM === this.base) {
            if (window.finCache[msg.FSYM]) {
              this.refreshMainChart(msg.FSYM);
            }
          }
        }
      } catch (err) {
        console.error(err);
      }
    })
  },
  methods: {
    setBase(base) {
      this.base = base;
      this.refreshMainChart(base);
    },
    refreshMainChart(base) {
      const now = Math.trunc(Date.now() / 1000);
      window.finCache[base] = window.finCache[base]
        .filter((msg) => {
          const {TS} = msg;
          if ((now - TS) > 120) {
            // console.log((now - TS), msg, new Date(TS * 1000))
            // ETH sends old data?
          }
          return (now - TS) < (this.lastMin * 60);
        });
      this.series = [{
        name: this.base,
        data: window.finCache[base]
          .map((msg) => {
            const dt = new Date(msg.TS * 1000)
            const dtStr = `${dt.getMinutes()}:${dt.getSeconds()}`
            return {
              x: dtStr,
              ts: msg.TS,
              y: msg.P
            }
          })
      }];
    },
  }
}
</script>

<style scoped>

.crypto-chart {
  margin: 2rem;
  padding: 1rem;
  height: 450px;
  background: #fff;
  border-radius: 4px / 8px;
}

</style>