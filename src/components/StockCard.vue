<template>
<section v-if="showCard">
    <div class="stock-card" >
        
        <h1>{{ticker.toUpperCase()}}  <small> ({{account}})</small></h1>
        <hr>

        <ul>
        <li><strong>Shares: </strong>{{stock.summary.shares}} <span class="right-side"><strong>Avg Price: </strong>{{stock.summary.stockAvgPrice}}</span></li>
        <li><strong>Unrealized: </strong>{{stock.summary.stockUnrealized}} <span class="right-side"><strong>Price: </strong>{{yfprice}}</span></li>
        <li><strong>Book: </strong><span style="padding-left: 5px;"/>{{stock.summary.bookValue}}</li>


        </ul>
        <hr>
        <ul>


        <li><strong>Stock Proceeds: </strong>{{stock.summary.stockProceeds}}</li>
        <li><strong>Stock Realized: </strong>{{stock.summary.stockRealized}}</li>

        </ul>
        <hr>
        <ul>

        <li><strong>Contracts: </strong>{{stock.summary.contracts}}</li>

        <li><strong>Option Proceeds: </strong>{{stock.summary.optionProceeds}}</li>
        <li><strong>Option Realized: </strong>{{stock.summary.optionRealized}}</li>

        </ul>
        <hr>
        <ul>
        <div v-if="stock.trades.length">
        <li><strong>Last trade: </strong>{{stock.trades[0].date_time.split(/T/)[0]}}</li>
        </div>

        <li><strong>Total Realized: </strong>{{stock.summary.totalRealized}}</li>
        
        <li v-if="false"><strong>Strike: </strong>{{stock.options[0].Strike}}
        <span class="right-side"><strong>Exp: </strong>{{stock.options[0].Exp}}</span></li>

        </ul>
        <hr>
        <ul>

        <li><strong>Expiration</strong></li>
        <li v-for="(option, i) in stock.options" :key="i">{{option.Symbol}}...... {{option.Realized.toFixed(0)}}
            <span class="right-side">{{option.Quantity}}</span></li>

        </ul>
        
    </div>

</section>
</template>

<script>
const accounts = ["RSP", "TFSA", "Margin"];
export default {
    props: {
        ticker: {
            type: String,
            default: 'csco'
        },
        account: {
            type: String,
            default: 'rsp'
        },
        yfprice : {
            type: Number,
            default: 0
        },
        path: {
            type: String,
            default: 'black:3000'
        },
        stockSummary: {
            default: {
                Realized: 0,
                Proceeds: 0,
                Basis: 0,
                Fees: 0
            }
        }
    },
    data() {
        return {
            stock: {
                summary: {
                    shares: 0,
                    contracts: 0,
                    bookValue: 0
                },
                stocks: [],
                options: [],
                trades: []
            },
            showCard: true,
            haveData: false
        }
    },
    watch: {
        ticker(tick) {
            if (accounts.includes(this.account)) {
                this.fetchStocks(this.ticker.split(/\./)[0]);
            }
            console.log('Watch ticker: ' + tick);
        },
        account() {
            console.log('Watch account: ' + this.account);
            if (accounts.includes(this.account)) {
                this.showCard = true;
                this.fetchStocks(this.ticker);
            } else {
                console.log('Watch list ' + this.account + ' selected');
                this.showCard = false;
            }
        },
        yfprice() {
            if (this.stock.summary.shares) {
                const shares = this.stock.summary.shares;
                const avgPrice = this.stock.summary.stockProceeds / shares;
                this.stock.summary.stockUnrealized = ((this.yfprice + avgPrice) * shares).toFixed(0);
                this.stock.summary.bookValue = (-avgPrice * shares).toFixed(0);
            }

        }
    },
    created() {
        this.fetchStocks(this.ticker);
    },
    methods: {
        fetchStocks(t) {
            this.stock = {
                summary: {
                    shares: 0,
                    contracts: 0,
                    stockProceeds: 0,
                    stockRealized: 0,
                    stockUnrealized: 0,
                    stockAvgPrice: 0,
                    optionProceeds: 0,
                    optionRealized: 0,
                    totalRealized: 0,
                    contractsArray: []
                },
                stocks: [],
                options: [],
                trades: []
            }
            const account = this.account.toLowerCase();
            const path = this.path;
            // const ib_api = `http://${path}/api/trades_${account}/stock/${t}`;
            const ib_api_trades = `http://${path}/api/trades_${account}/trades/${t}`;
            // const ib_api = `http://134.41.119.151:5000/api/trades_${account}/stock/${t}`;
            console.log('StockCard ' + ib_api_trades);

            fetch(ib_api_trades)
            .then( res => res.json())
            .then((data) => {
            this.stock.trades = data;
            console.log('api response...');
            console.log(data);

            this.haveData = true;

            for (const trade of data) {
                if (trade.Category == 'Stocks') {
                    this.stock.stocks.push(trade);
                    this.stock.summary.shares += parseInt(trade.Quantity);
                    this.stock.summary.stockProceeds += parseInt(trade.Proceeds);
                    this.stock.summary.stockRealized += parseInt(trade.Realized);
                    if (this.stock.summary.shares) {
                        const shares = this.stock.summary.shares;
                        const avgPrice = this.stock.summary.stockProceeds / shares;
                        this.stock.summary.stockAvgPrice = -avgPrice.toFixed(2);
                        
                        } else {
                        this.stock.summary.stockAvgPrice = 0;
                        this.stock.summary.stockUnrealized = 0;    
                        }
                } else {
                    this.stock.options.push(trade);
                    this.stock.summary.contracts += parseInt(trade.Quantity);
                    this.stock.summary.contractsArray.push(parseInt(trade.Quantity));
                    this.stock.summary.optionProceeds += parseInt(trade.Proceeds);
                    this.stock.summary.optionRealized += parseInt(trade.Realized);
                }
                // console.log(trade);

            }
            this.stock.summary.totalRealized = this.stock.summary.stockRealized + this.stock.summary.optionRealized;
            
            console.log('Stocks => ', this.stock);

            })
            .catch((err) => {
            console.log('Error: ', err.message);
            })  

        }
    }
    
}
</script>

<style scoped>
h1, h3, p {
    text-align: left;
}
.tab {
  padding-left: 40px;
}
.tab2 {
  padding-left: 80px;
}
h4, h5 {
    text-align: right;
}
.stock-card {
    text-align: center;
    color: black;
    border: 0px solid rgb(87, 72, 44);
}
ul {
  padding: 0;
  margin: 15px;
  list-style: none;
}
li {
    list-style-type: none;
    text-align: justify;
}


.right-side {
    float: right;
}

</style>