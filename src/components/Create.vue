<template>
  <div class="overflow-x-hidden" id="create-section">
      <Navbar :showDownloadButton="true"></Navbar>
      <CreateHero/>
      <CreateGenerateAndPrint/>
      <Footer/>
  </div>
</template>

<script>
import Vue from "vue";
import Navbar from "./Navbar.vue";
import CreateHero from "./CreateHero.vue";
import CreateGenerateAndPrint from "./CreateGenerateAndPrint.vue";
import Footer from "./Footer.vue";

export default Vue.extend({
  name: "Create",
  components: {
    Navbar,
    CreateHero,
    CreateGenerateAndPrint,
    Footer
  }
});
</script>

<style lang="scss">
@import "../assets/css/main.scss";
</style>