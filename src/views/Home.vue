<template>
  <div class="home">
    <!-- <img alt="Vue logo" src="../assets/logo.png" />
    <HelloWorld msg="Welcome to Your Vue.js App" /> -->
    <whats-my-ip class="whats-my-ip"
    v-if="true"
    @api-path="setPath($event)"></whats-my-ip>

    <app-header title="Stock Summary"></app-header>



  </div>
</template>

<script>
// @ is an alias to /src
// import HelloWorld from "@/components/HelloWorld.vue";
import AppHeader from '@/components/AppHeader.vue';
import WhatsMyIp from '@/components/WhatsMyIp.vue';

export default {
  data () {
    return {
      path: "black:3000"
    }

  },
  methods: {
    setPath(path) {
      this.path = path;
    }


  },
  name: "Home",
  components: {
    AppHeader,
    WhatsMyIp
    // HelloWorld
  }
};
</script>
