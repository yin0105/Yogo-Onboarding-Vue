<template>
  <div class="relative__container--100">
    <div class="login modal__fixed--container">
    </div>
  </div>
</template>


<script>

  export default {
    name: 'init',

    created() {
      console.log("=== Init::  created:: locale = ", this.$route.query, this.locale);
      console.log("=== i18n = ", this.$i18n.locale);
      if (this.$route.query.locale) {
        this.$store.dispatch('updateLocale', this.$route.query.locale);  
      } 
    },
  }

</script>
