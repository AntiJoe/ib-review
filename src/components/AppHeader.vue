<template>
<section class="header">
    <h1 id="header-text">{{title}}</h1>
    <hr>

</section>   
</template>

<script>


export default {
    props: ['title'],
    name: 'Header'
}
</script>

<style scoped>
.header {
    text-align: center;
    font-size: 1.1 rem;
    border: 0px solid orange;
    color: firebrick;
}
h1 {
    text-align: center;
}
</style>