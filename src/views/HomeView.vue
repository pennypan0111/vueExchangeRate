<script>
  import axios from 'axios';
  import APIconfig from '../../APIconfig.js'

  export default{
    data(){
      return{
          TWD: null,
          JPY: null,
          USD: null,
          JYPExchangeRate: null,
          USDExchangeRate: null,
      }
    },
    mounted(){
      axios.get(`https://v6.exchangerate-api.com/v6/${APIconfig}/latest/USD`).then((response)=>{
        this.JPY = response.data.conversion_rates.JPY;
        this.USD = response.data.conversion_rates.USD;
        this.USDExchangeRate = (response.data.conversion_rates.USD) / (response.data.conversion_rates.TWD); // 1台幣換成美金
        this.JYPExchangeRate = 1 / ((response.data.conversion_rates.USD / response.data.conversion_rates.JPY) * response.data.conversion_rates.TWD); // 1台幣換成日圓
      })
    },
    computed:{
      JPY:{
        get(){
            return Number.parseFloat(Number(this.TWD) * this.JYPExchangeRate).toFixed(2);
        },
      },
      USD:{
        get(){
            return Number.parseFloat(Number(this.TWD) * this.USDExchangeRate).toFixed(2);
        },
      },
      JPYFormattedExchangeRate() {
          return Number.parseFloat(this.JYPExchangeRate).toFixed(2);
      },
      USDFormattedExchangeRate() {
          return Number.parseFloat(this.USDExchangeRate).toFixed(3);
      },
    },
  }
</script>

<template>

<div class="d-flex flex-column justify-content-center align-items-center content">
        <div class="text-center title ">
            <h1 class="title-CN">幣值換算</h1>
            <p class="title-EG">Currency Conversion</p>
        </div>

        <div class="d-flex flex-column flex-xl-row justify-content-center align-items-center w-100 circle-group">

            <p class="reMark d-lg-none">目前即時匯率約：1台幣={{ JPYFormattedExchangeRate }}日圓={{ USDFormattedExchangeRate }}美元</p>

            <div class="circleBg col-4 d-flex flex-column justify-content-center align-items-center">
                <p class="countryName">台灣</p>
                <div class="flag">
                    <img src="../../public/taiwan-flag.png">
                </div>
                <input class="amount text-center align-middle" type="text" placeholder="請輸入金額" v-model="TWD">
                <p class="currency-value">(新台幣)</p>
            </div>

            <div class="equal">=</div>

            <div class="circleBg col-4 d-flex flex-column justify-content-center align-items-center">
                <p class="countryName">日本</p>
                <div class="flag">
                    <img src="../../public/japan-flag.png">
                </div>
                <input class="amount text-center align-middle" type="text" v-model="JPY" readonly>
                <p class="currency-value">(日圓)</p>
            </div>

            <div class="equal">=</div>

            <div class="circleBg col-4 d-flex flex-column justify-content-center align-items-center">
                <p class="countryName">美國</p>
                <div class="flag">
                    <img src="../../public/US-flag.png">
                </div>
                <input class="amount text-center align-middle" type="text" v-model="USD" readonly>
                <p class="currency-value">(美元)</p>
            </div>
            
        </div>

        <p class="reMark d-none d-lg-block">目前即時匯率約：1台幣={{ JPYFormattedExchangeRate }}日圓={{ USDFormattedExchangeRate }}美元</p>
    </div>

</template>
