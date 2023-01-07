<template>
  <div id="app">
    <side></side>
    <modal id="modal"></modal>
  </div>
</template>

<style lang="scss">
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #2c3e50;
  }

  // #modal {
  //   z-index: 1060;
  //   position: fixed;
  // }

  // #sidebar-1 {
  //   z-index: 2001 !important;
  // }

  // #modal.modal.fade.show {
  //   z-index: 1050 !important; 
  // }

  // #false-table {
  //   z-index: 21478954 !important;
  // }

</style>

<script>
  import side from "@/components/side.vue";
  import modal from "@/components/modal.vue";
  export default {
  components: {
    side,
    modal
  }
}
</script>
