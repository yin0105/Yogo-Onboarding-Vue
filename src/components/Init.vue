<template>
  <div class="relative__container--100">
    <div class="login modal__fixed--container">
      <h3>Here is On boarding</h3>
    </div>
  </div>
</template>


<script>
  import router from '@/router';

  export default {
    name: 'init',

    created() {
      if (this.$route.query.locale) {
        this.$store.dispatch('updateLocale', this.$route.query.locale);  
      }
      router.push({ name: 'Signup', query: this.$route.query }); 
    },
  }

</script>
