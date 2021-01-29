<template>
    <div id="api-data">
    <ul>
    <li v-for="stock in underlyings" :key="stock.Underlying" 
    class="ticker"> {{ stock.Underlying }} </li> 
    </ul>  
        
    </div>
</template>

<script>
const account = "tfsa";
const ib_api = `http://black:3000/api/trades_${account}/stocks`;

export default {
    underlyings: [],

    mounted() {
        this.fetchStocks();
    },

methods: {
    fetchStocks() {
        fetch(ib_api)
        .then( (res) => {
        return res.json();
        })
        .then((data) => {
        this.underlyings = data;
        console.log(data);
        })
        .catch((err) => {
        console.log('Error: ', err.message);
        })  
        }
}

}
</script>