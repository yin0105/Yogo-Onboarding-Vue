<template>
  <div
    class="min-h-screen flex items-center justify-center md:bg-gray-50 py-12 px-4 sm:px-6 lg:px-8">
    <div class="max-w-md w-full">

      <div v-if="success" class="md:bg-white md:border rounded border-gray-200 py-8 md:py-10 sm:px-6 md:px-8">
        <h2 class="mt-6 text-center">
          <p>{{ $t('login.clientSignupNotification') }}</p>            
        </h2>
        
        <div style="width: 350px; text-align: center; margin: auto; margin-top: 40px; font-size: 20px">
            <p>{{ $t('login.hiYogo') }}.</p>
            <p>{{ $t('login.signedUpDemo') }}:</p>
            <p>{{ $t('global.ID') }}: {{id}}</p>
            <p>{{ $t('global.StudioName') }}: {{clientName}}</p>
            <p>{{ $t('login.userName') }}: {{userName}}</p>
            <p>{{ $t('global.Email') }}: {{email}}</p>
        </div>
      </div>
      <div  class="md:bg-white md:border rounded border-gray-200 py-8 md:py-10 sm:px-6 md:px-8">
        <div style="width: 350px; text-align: center; margin: auto; font-size: 20px">
            <p>{{ errorMessage }}</p>
        </div>
      </div>
    </div>

  </div>
</template>

<script>

import { mapGetters } from 'vuex';
import YogoApi from '@/gateways/YogoApi';
import ModalLogo from './ModalLogo';
import axios from 'axios';

export default {
  data() {
    return {
      id: '',
      clientName: '',
      userName: '',
      email: '',
      success: false,
      error: false,
      errorMessage: '',
    }
  },
  components: {
    ModalLogo,
  },
  created: async function() {    
    if (this.$route.query.token) {
      console.log("query = ", this.$route.query);
      const token = this.$route.query.token;
      const response = await YogoApi.get(`/client-signing-up/confirm?token=${token}`);
      if (response.error) {        
        this.error = true;
        this.success = false;
        this.errorMessage = response.error;
      } else {
        this.success = true;
        this.error = false;
      }
    }
  }
};

</script>

<style lang="stylus" scoped="true">

label
  display block
  height 100%

.router-link-active
  background none

input.error
  border-color: red;

</style>
