<template>
<section>
<div class="watch-list">

<h1>Watch Lists</h1>
<hr>
<label for="select-watch-list"><strong>Select List: </strong></label>
<select @change="changeList()" v-model="key">
    <option v-for="(list, i) in watchLists.lists" :key="i" :value="list">{{ list.name }}</option>
</select>
<!-- <button>Update List</button> -->
<hr>

<form @submit.prevent="submitForm" >
    <label for="new-ticker"><strong> Ticker: </strong></label>
    <input id="new-ticker" name="new-ticker" style="text" v-model="newTicker" size="10"/>
    <button>Add</button>
</form>
<hr>
    <ul>
        <li v-for="(ticker, i) in tickers"
        :key="i"
        @click="showStock(ticker)">{{ ticker.toUpperCase() }}</li>           
    </ul>

</div>  
</section>
</template>

<script>
import watchListsJSON from '../assets/watchLists/watchLists.json';

export default {
    data () {
        return {
            key: '',
            newTicker: '',
            selectedStock: '',
            watchLists: {},
            tickers: []
        }
    },
    created() {
        const wljson = JSON.stringify(watchListsJSON);
        const watchLists = JSON.parse(wljson);

        console.log(watchLists);
        console.log(watchLists.lists[1].name);

        this.watchLists = watchLists;

        
        
    },
    methods: {
        changeList() {
            console.log('Change List to: ', this.key);
            this.loadList(this.key);
            this.$emit('selected-account', this.key.name);

        },
        loadList(listName) {
            console.log('Load List => ', listName.name);
            this.tickers = listName.tickers;
            this.tickers.sort();

            // console.log(wlists);
        },
        showStock(stock) {
            this.$emit('selected-stock', stock );
            this.selectedStock = stock;
        },
        submitForm() {
            console.log('New Ticker: ' + this.newTicker);
            this.$emit('selected-stock', this.newTicker );
            this.tickers.push(this.newTicker);
            this.newTicker = '';

        }
    } 
}
</script>

<style scoped>
.watch-list {
    text-align: left;
    /* color:rgb(219, 48, 211); */
    border: 0px solid rgb(87, 72, 44);
    border-radius: 1em;
    margin: 0px;
    /* width: 200px; */
}
ul {
  padding: 0;
  margin: 15px;
  list-style: none;
}


</style>