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
     
      <div v-if="error" class="md:bg-white md:border rounded border-gray-200 py-8 md:py-10 sm:px-6 md:px-8">
        <h2 class="mt-6 text-center">
          <p v-if="errorType == 'expired'">{{ $t('login.linkExpired') }}</p>            
          <p v-if="errorType == 'invalid'">{{ $t('login.linkInvalid') }}</p>            
        </h2>
        
        <div style="width: 350px; text-align: center; margin: auto; margin-top: 40px; font-size: 20px">
          <p v-if="errorType == 'expired'">{{ $t('login.linkExpiredComment') }}</p>            
          <p v-if="errorType == 'invalid'">{{ $t('login.linkInvalidComment') }}</p>            
        </div>

        <button class="btn-primary mt-8 w-full" type="submit">
          {{ $t('login.startMyFreeDemo') }}
        </button>
      </div>

      <div v-if="loading" class="md:bg-white md:border rounded border-gray-200 py-8 md:py-10 sm:px-6 md:px-8">
        <pulse-loader :loading="loading" :color="color" :size="size"></pulse-loader>
      </div>

      
    </div>

  </div>
</template>

<script>

import { mapGetters } from 'vuex';
import YogoApi from '@/gateways/YogoApi';
import ModalLogo from './ModalLogo';
import axios from 'axios';
import PulseLoader from 'vue-spinner/src/PulseLoader.vue'

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
      errorType: '',
      
      color: '#5bc0de',
      size: '45px',
      margin: '2px',
      radius: '2px',
      loading: false
    }
  },
  components: {
    ModalLogo,
    PulseLoader,
  },
  created: async function() {    
    if (this.$route.query.token) {
      console.log("query = ", this.$route.query);
      const token = this.$route.query.token;
      this.loading = true;
      const response = await YogoApi.get(`/client-signing-up/confirm?token=${token}`);

      if (response.error) {        
        this.error = true;
        this.success = false;
        this.loading = false;
        if (response.error == "The provided token is expired.") {
          this.errorType = "expired";
        } else if (response.error == "The provided token is invalid, or already used up." || response.error == "This email address already exits.") {
          this.errorType = "invalid";
        }
      } else {
        this.loading = false;
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
