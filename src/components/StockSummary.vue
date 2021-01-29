<template>
<li>
    <h2>{{ stock.name}}</h2>
    <button @click="toggleDetails">Show Details</button>
    <ul v-if="detailsVisible">
        <li><strong>Ticker: </strong> {{ stock.ticker }} </li>
        <li><strong>Price: </strong> {{ stock.price }} </li>
            
    </ul>
</li>
</template>

<script>
export default {
    data() {
        return {
            detailsVisible: false,
            stock: {
                ticker: 'CSCO',
                price: 44.54,
                name: 'Cisco Systems',
                account: 'rsp',
                positionSize: 200
            }
        }
    },
    methods: {
        toggleDetails() {
            this.detailsVisible = !this.detailsVisible;
        }
        

    },
}
</script>

<style scoped>
li {
    list-style-type: none;
}

</style>