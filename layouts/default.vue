<template>
  <v-app
    transition="slide-y-transition"
    class="app-container"
  >
    
   <home />
  </v-app>
</template>

<script>

import home from '~/pages/home.vue'

export default {
  data() {
    return {
      
    };
  },
  computed: {
    
  },

  methods: {

  },
  mounted() {
  },
  components: { home },
};
</script>

<style lang="scss">

</style>
