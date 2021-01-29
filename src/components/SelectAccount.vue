<template>
    <div  class="account-card">
        <h1>Account</h1>
        <form action="">
            <select name="account-selection" 
            id="account-selection" 
            v-model="account" 
            @change="selectAccount">
                <option value="rsp">RSP</option>
                <option value="tfsa">TFSA</option>
                <option value="margin">Margin</option>
            </select>
        </form>
        
    </div>
</template>

<script>
export default {
    data() {
        return {
            account: 'rsp'
        };
    },
    methods: {
        selectAccount() {
            console.log('Account: ' + this.account);
            this.$emit('account-selected', this.account)
        }
}
};
</script>

<style scoped>
.account-card {
    text-align: center;
    color: darkgreen;
    border: 3px solid rgb(87, 72, 44);
    margin: 0px;
    height: 150px;
}
h1, h3 {
    text-align: left;
}
form {
    text-align: left;
}

</style>