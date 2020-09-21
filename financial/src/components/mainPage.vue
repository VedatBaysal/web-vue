<template>
  <div class="hello">
    <h1>Showing Income Statement {{this.stock}}</h1>
    <form @submit.prevent="updateStock">
      <input type="text" v-model="stock" name="stock" placeholder="Enter Ticker"> 
      <input type="submit" value="Submit" class="btn-primary m-2">
    </form>
    <div class="container" v-if="loaded">
      {{this.stock}}
      <div class="stock_name"> {{stockprice}}</div>
    </div>
    <div class="row" v-if="loaded">
      <div class="col-md-5 ml-5">
        <b-table sticky-header hover :items="items" :fields="fields"></b-table>
      </div>
      <div class="col-md-5">
        <chart :options="ChartOptionsBar"></chart>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: 'mainPage',
  props: {
    msg: String
  },
  data() {
    return {
      url: '',
      allFinancials: [],
      loaded: false,
      financials: [],
      stock: '',
      ChartOptionsBar: {},
      stockprice: '',
    }
  },
  methods: {
    plotStock(allFinancials) {
      this.ChartOptionsBar = {
        xAxis: {
          data: [this.allFinancials[0].date,this.allFinancials[1].date, this.allFinancials[2].date, this.allFinancials[3].date ]
        },
        yAxis: {
          type: 'value',
        },
        series: {
          type: 'bar',
          data: [this.allFinancials[0].revenue/1000000, this.allFinancials[1].revenue/1000000,this.allFinancials[2].revenue/1000000,this.allFinancials[3].Revenue/1000000,]
        }, 
        title: {
          text:'Yearly Revenues',
          x: 'center',
          textStyle: {
            fontSize:24
          }
        },
      }
    },
    stockPrice() {
      this.url = 'https://financialmodelingprep.com/api/v3/historical-chart/5min/' + this.stock + '?apikey=demo';
      axios.get(this.url)
      .then( res => {
        this.stockprice = res.data[0].volume,
        console.log(this.stockprice)
      })
      .catch( err => console.log(err))
    },
    formatNumber(number) {
      number = (number/1000000).toFixed(2).replace('.',',');
      return number.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".")
    },
    updateStock() {
      this.url = 'https://financialmodelingprep.com/api/v3/income-statement/' + this.stock + '?period=quarter&limit=400&apikey=demo'
      axios.get(this.url)
      .then(res => { 
        this.allFinancials = res.data,
        this.loaded = true,
        this.financials = res.data[0],
        this.fields = ['item','in_Millions','in_Millions1'],
        this.items = [
          { in_Millions: this.allFinancials[0].date, item: Object.keys(this.financials)[0], in_Millions1:this.allFinancials[1].date},
          { in_Millions: this.formatNumber(this.allFinancials[0].revenue) , item: Object.keys(this.financials)[1],in_Millions1: this.formatNumber(this.allFinancials[1].revenue )},
          { in_Millions: this.formatNumber(this.allFinancials[0].costOfRevenue), item: Object.keys(this.financials)[3], in_Millions1: this.formatNumber(this.allFinancials[1].costOfRevenue)},
          { in_Millions: this.formatNumber(this.allFinancials[0].operatingExpenses), item: Object.keys(this.financials)[7], in_Millions1: this.formatNumber(this.allFinancials[1].operatingExpenses)},
          { in_Millions: this.formatNumber(this.allFinancials[0].operatingIncome), item: Object.keys(this.financials)[8], in_Millions1: this.formatNumber(this.allFinancials[1].operatingIncome)},
          { in_Millions: this.formatNumber(this.allFinancials[0].interestExpense), item: Object.keys(this.financials)[9], in_Millions1: this.formatNumber(this.allFinancials[1].interestExpense)},
          { in_Millions: this.formatNumber(this.allFinancials[0].incomeBeforeTax), item: Object.keys(this.financials)[10], in_Millions1: this.formatNumber(this.allFinancials[1].incomeBeforeTax)},
          { in_Millions: this.formatNumber(this.allFinancials[0].incomeTaxExpense), item: Object.keys(this.financials)[11],in_Millions1: this.formatNumber(this.allFinancials[1].incomeTaxExpense)},
          { in_Millions: this.formatNumber(this.allFinancials[0].netIncome), item: Object.keys(this.financials)[14], in_Millions1: this.formatNumber(this.allFinancials[1].netIncome)},
         ]
        this.plotStock(this.allFinancials)
        this.stockPrice()
      })
      .catch (err => console.log(err))
    }
  }
}
</script>

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
.b-table-sticky-header {
  
    max-height: unset;
}
stock_name {
  padding: 10px;
  
}
</style>
