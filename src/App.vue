<template>
  <v-app>
    <v-main>
      <div id="main-div">
        <Navbar />
        <router-view />
        <Footer />
      </div>
    </v-main>
  </v-app>
</template>


<script>
import Navbar from "./components/Navbar.vue";
import Footer from "./components/Footer.vue";

export default {
  components: {
    Navbar,
    Footer,
  },
  data() {
    return {};
  },
};
</script>

<style>
* {
  font-family: Helvetica;
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

.main-container {
  margin: 50px;
  min-height: 350px;
}

h1 {
  width: 350px;
  margin: 0 auto;
  text-align: center;
  font-size: 20px;
  margin-bottom: 25px;
  color: #222;
}

#main-div {
  width: 100%;
  height: 100%;
  margin: auto;
  background-color: #eeeeee;
}
</style>
