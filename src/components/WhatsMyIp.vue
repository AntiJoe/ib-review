<template>
    <div v-if="haveData">
        <p ><strong>WAN IP: </strong> {{here.ip}} 
        <span class="right-side"><strong>API Path: </strong> {{here.path}}</span></p> 
    </div>
</template>

<script>
export default {
    data() {
        return {
            here: {},
            client: {
                ip: '',
                ipArray: [],
                onChelseaLan: false,
                port: 3000,
                path: 'black:3000'
            },
            haveData: false
        }
    },
    created() {
        this.fetchIP();
    },
    methods: {
        fetchIP() {
            const client_api = `https://api64.ipify.org/?format=json`;
            fetch(client_api)
            .then( res => res.json())
            .then((data) => {
            this.client = data;
            console.log('Client IP: ', data);
            this.whereAmI(data.ip)
            // this.$emit('api-path', this.client.path);
            })
            .catch((err) => {
            console.log('Error: ', err.message);
            }) 

        },
        whereAmI(ip) {
            const chelseaIP = '134.41.119.151';
            console.log('where am I? ip: ' + ip);
            this.here.ip = ip;
            this.here.ipArray = this.here.ip.split(/\./);
            this.here.onChelseaLan = (this.here.ip == chelseaIP ? true : false);
            this.here.port = (this.here.ip == chelseaIP ? 3000 : 5000);
            this.here.path = (this.here.ip == chelseaIP ? 'black' : chelseaIP) + ':' + this.here.port;
            console.log("API is here: " + JSON.stringify(this.here.path));
            this.$emit('api-path', this.here.path);
            this.haveData = true;
        }

    }
}
</script>

<style scoped>
h1, h3, p {
    text-align: left;
}
.right-side {
    float: right;
}

</style>