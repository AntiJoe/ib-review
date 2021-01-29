<template>
    <div class="account-summary">
        <h1>Account Monthly Summary</h1>
        <template v-if="monthsCategories.stocks">
            <hr>
        <h3 >Stocks</h3>
        <ul>
            <li v-for="month in monthsCategories.stocks" :key="month.Proceeds"
            class="monthly-li">
            {{month.Month}} {{month.Realized.toFixed(0)}}
            </li>
        </ul>
        </template>

        <template v-if="monthsCategories.options">
        <hr>
        <h3 >Options</h3>
        <ul>
            <li v-for="month in monthsCategories.options" :key="month.Proceeds"
            class="monthly-li">
            {{month.Month}} {{month.Realized.toFixed(0)}}
            </li>
        </ul>
        </template>

        <template v-if="monthsCategories.futures.length">
        <hr>    
        <h3 >Futures</h3>
        <ul>
            <li v-for="month in monthsCategories.futures" :key="month.Proceeds"
            class="monthly-li">
            {{month.Month}} {{month.Realized.toFixed(0)}}
            </li>
        </ul>
        </template>
        
    </div>
</template>

<script>
export default {
    props: {
        account: {
            type: String,
            default: 'rsp'
        }
    },
    data() {
        return {
            haveData: false,
            months: {},
            monthsCategories: {
                name: 'Monthly Summary by Security Categories',
                stocks: [],
                options: [],
                futures: [],
                others: []
            }
        }
    },
    watch: {
        account() {
            this.monthsCategories = {
                name: 'Monthly Summary by Security Categories',
                stocks: [],
                options: [],
                futures: [],
                others: []
            };
            this.fetchStocks();
            console.log('Monthly account: ' + this.account);
        }
    },

    methods: {
        fetchStocks() {
            const account = this.account;
            const ib_api = `http://black:3000/api/trades_${account}/monthly`;
            console.log(ib_api);

            fetch(ib_api)
            .then( res => res.json())
            .then((data) => {
            this.months = data;
            // console.log(data);
            this.haveData = true;
            for (const record of data) {
                // console.log(record);
                if (record.Category == "Stocks") {
                    this.monthsCategories.stocks.push(record);
                }
                else if (record.Category == "Equity and Index Options") {
                    this.monthsCategories.options.push(record);
                }
                else if (record.Category == "Futures") {
                    this.monthsCategories.futures.push(record);
                } 
                else {
                    this.monthsCategories.others.push(record);
                }
            }
            console.log(this.monthsCategories)
            })
            .catch((err) => {
            console.log('Error: ', err.message);
            })  

        }
    },
    
}
</script>



<style scoped>


.account-summary {
    text-align: center;
    /* color:rgb(219, 48, 211); */
    border: 3px solid rgb(87, 72, 44);
    margin: 0px;
}
li {
    list-style-type: none;
    text-align: left;
}
h1, h3 {
    text-align: left;
}


</style>