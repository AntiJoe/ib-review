<template>
<section class="stock-list">
    <h2>Stock List  {{numberOfStocks()}}</h2>
    <div v-if="haveData">
    <ul >
    <li v-for="stock in underlyings" :key="stock.Underlying"  
    class="ticker"
    @click="showStock(stock)"> {{ stock.Underlying }} </li> 
    </ul>   
    <p>Selected: {{selectedStock.Underlying}}</p>
    </div>
    <!-- </Suspense> -->
</section>
</template>

<script>




export default {
    props: {
        account: {
            type: String,
            default: 'rsp'
        }
    },
    data () {
        return {
            // account: 'rsp',
            underlyings: [],
            selectedStock: {},
            haveData: false
        }
        
    },
    watch: {
        account() {
            this.fetchStocks();
            console.log('watch: ');
        }
    },
    
    created() {
        this.fetchStocks();
    },

    methods: {
        fetchStocks() {
            const account = this.account;
            const ib_api = `http://black:3000/api/trades_${account}/stocks`;
            // const ib_api = `http://134.41.119.151:5000/api/trades_${account}/stocks`;
            fetch(ib_api)
            .then( res => res.json())
            .then((data) => {
            this.underlyings = data;
            console.log(data);
            this.haveData = true;
            })
            .catch((err) => {
            console.log('Error: ', err.message);
            })  
            },
        numberOfStocks() {
            return this.underlyings.length;
        },
        showStock(stock) {
            const u = stock.Underlying;
            this.$emit('selected-stock', u );
            this.$emit('selected-stock-currency', (stock.Currency == 'CAD') ? u + ".TO" : u );
            this.$emit('selected-stock-summary', stock);
            console.log(stock);
            this.selectedStock = stock;
        }
    
    }

}
</script>

<style scoped>
.stock-list {
    text-align: center;
    color:darkgreen;
    border: 2px solid black;
    width: 200px;
    border-radius: 1em;
    margin: 0px;
    
}
li {
    list-style-type: none;
    text-align: left;
}
.ticker {
    color: blue;
}


</style>
