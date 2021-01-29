<template>
<section>
    <div  v-if="haveData" class="yahoo-finance">
        <h1>{{stock.price.shortName}}</h1>
        <hr>
        <ul>
            <li><strong>Ticker: {{stock.price.symbol}}</strong> ({{stock.price.exchangeName}}) 
            <span class="right-side" v-if="stock.price.exchangeDataDelayedBy"><strong>Delay:</strong> 
            {{stock.price.exchangeDataDelayedBy}} min</span></li>


            <li><strong>Price:</strong> {{stock.price.regularMarketPrice.toFixed(2)   }}  
                <span class="right-side"><strong style="margin-left:1em"> Change:</strong> {{stock.price.regularMarketChange.toFixed(2)}}
                 ( {{(100*stock.price.regularMarketChangePercent).toFixed(2)}}% )</span></li>
            <li><strong>52 Week High:</strong> {{ stock.summaryDetail.fiftyTwoWeekHigh.toFixed(2) }}  
                <span class="right-side"><strong style="margin-left:2em">52 Week Low:</strong> {{stock.summaryDetail.fiftyTwoWeekLow.toFixed(2)}}
                </span></li>

            

            <li><strong>200 Day Avg: </strong>{{stock.summaryDetail.twoHundredDayAverage.toFixed(2)}} 
            <span class="right-side"><strong style="margin-left:2em">50 Day Avg: </strong>{{stock.summaryDetail.fiftyDayAverage.toFixed(2)}}</span></li>
            

            <li><strong>Currency: </strong>{{stock.price.currency}}</li>
        </ul>
        <hr>
        <div>
        <h3><strong>Financials</strong></h3>
        <ul>
            <!-- <li v-if="stock.summaryDetail.marketCap"><strong>Market Cap: </strong> {{stock.summaryDetail.marketCap}}</li> -->
            <li v-if="stock.summaryDetail.forwardPE"><strong>Forward  PE:</strong> {{ (stock.summaryDetail.forwardPE).toFixed(1) }} </li>
            <li v-if="stock.summaryDetail.trailingPE"><strong>Trailing PE:</strong> {{ (stock.summaryDetail.trailingPE).toFixed(1) }} </li>
            <li>...</li>
        </ul>
        <hr>
        </div>
        <div v-if="stock.summaryDetail.dividendRate">
        <h3 ><strong>Dividends</strong></h3>
        <ul>
            <li><strong>Yield:</strong> {{ (100*stock.summaryDetail.dividendYield).toFixed(2) }}% </li>
            <li><strong>Ex Div:</strong> {{(stock.calendarEvents.exDividendDate.split('T')[0])}}</li>
            <li><strong>Div Date:</strong> {{stock.calendarEvents.dividendDate.split('T')[0]}}</li>
            <li><strong>Payout Ratio:</strong> {{(100*stock.summaryDetail.payoutRatio).toFixed(1) }}%</li>
        </ul>
        </div>
        
    </div>
</section>
</template>

<script>
// const account = "rsp";
export default {
    props: {
        ticker: {
            type: String,
            default: 'csco'
        },
        path: {
            type: String,
            default: 'black:3000'
        }
    },
        
    data() {
        return {
            stock: {},
            passedStock: {},
            haveData: false
        }
},
watch: {
        ticker(tick) {
            this.fetchStocks(tick);
            console.log('YF ticker watcher recieved: ' + tick);
        }

    },
created() {
        this.fetchStocks('csco');
    },
methods: {
        fetchStocks(t) {
            const path = this.path;
            const ib_api = `http://${path}/api/yf/${t}`;
            fetch(ib_api)
            .then( res => res.json())
            .then((data) => {
            this.stock = data;
            console.log('YF returned data object: ', data);
            this.haveData = true;
            this.$emit('yf-last-price', data.price.regularMarketPrice);
            })
            .catch((err) => {
            console.log('Error: ', err.message);
            })  
            }
    }
}

</script>

<style  scoped>
ul {
  padding: 0;
  margin: 15px;
  list-style: none;
}
.right-side {
    float: right;
}

</style>