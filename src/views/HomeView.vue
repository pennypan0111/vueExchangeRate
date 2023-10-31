<script>
  import axios from 'axios';

  export default{
    data(){
      return{
          TWD: null,
          JPY: null,
          USD: null,
          JYPExchangeRate: null,
          USDExchangeRate: null,
          upDateTime: '',
      }
    },
    mounted(){
      axios.get(`https://v6.exchangerate-api.com/v6/${import.meta.env.VITE_APIconfig}/latest/USD`).then((response)=>{
        this.JPY = response.data.conversion_rates.JPY;
        this.USD = response.data.conversion_rates.USD;
        this.USDExchangeRate = (response.data.conversion_rates.USD) / (response.data.conversion_rates.TWD); // 1台幣換成美金
        this.JYPExchangeRate = 1 / ((response.data.conversion_rates.USD / response.data.conversion_rates.JPY) * response.data.conversion_rates.TWD); // 1台幣換成日圓

        // const unixTimestamp = response.data.time_last_update_unix-2;
        const unixTimestamp = response.data.time_last_update_utc;
        const date = new Date(unixTimestamp); // 乘以 1000 將秒轉換為毫秒

        // 使用 Date 物件的方法取得年、月、日、時、分和秒
        const year = date.getFullYear();
        const month = date.getMonth() + 1; // 月份是從 0 開始的，所以要加 1
        const day = date.getDate();
        const hours = date.getHours();
        const minutes = date.getMinutes();
        const seconds = date.getSeconds();

        // 將結果格式化成想要的字串
        const formattedDate = `${year}/${month}/${day} 上午 ${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;

        this.upDateTime = formattedDate;

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

            <p class="reMark d-lg-none mb-0 text-center word-break-all">目前即時匯率約：1台幣={{ JPYFormattedExchangeRate }}日圓={{ USDFormattedExchangeRate }}美元</p>
            <span class="text-light d-lg-none mb-4 text-center word-break-all">最後更新時間：{{ this.upDateTime }} (每24小時更新一次)</span>

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

        <p class="reMark d-none d-lg-block mb-0">目前即時匯率約：1台幣={{ JPYFormattedExchangeRate }}日圓={{ USDFormattedExchangeRate }}美元</p>
        <span class="text-light d-none d-lg-block">最後更新時間：{{ this.upDateTime }} (每24小時更新一次)</span>
    </div>

</template>
