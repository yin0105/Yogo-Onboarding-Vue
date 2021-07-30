<template>
  <div
    class="min-h-screen flex items-center justify-center md:bg-gray-50 py-12 px-4 sm:px-6 lg:px-8">
    <div class="w-full" v-bind:style="{maxWidth: '600px'}">

      <div v-if="success" class="md:bg-white md:border rounded border-gray-200 py-8 md:py-10 sm:px-6 md:px-8">
        <h2 class="mt-6 text-center">
          <p>{{ $t('email.YourYogoDemoReady') }}!</p>            
        </h2>
        
        <div style="width: 500px; margin: 40px 30px 20px; font-size: 20px">

            <p>{{ $t('email.Dear') }} {{first_name}}.</p>  
            {{ $t('email.YourYogoDemoReady') }}.  
            {{ $t('email.PleaseAllowIntroduction') }}  
            {{ $t('email.CanLogWithiEmailPassword') }}  

            <h3 v-bind:style="styleObjectH3">{{ $t('email.AdminModule') }}: {{admin_module_dashboard_link}}</h3> 
            {{ $t('email.WhereKeepTrack') }}  
            {{ $t('email.CustomersClassesEtc') }}  

            <h3 v-bind:style="styleObjectH3">{{ $t('email.CustomerModule') }}: {{frontend_my_profile_link}}</h3> 
            {{ $t('email.WhereBuyRegister') }}  

            <h3 v-bind:style="styleObjectH3">{{ $t('email.WebsiteWidget') }}</h3> 
            {{ $t('email.ModulesEasilyEmbeded') }}  
            {{ $t('email.WayYourCustomers') }}  

            <h3 v-bind:style="styleObjectH3">{{ $t('email.TeacherModule') }}: {{teacher_my_schedule_link}}</h3> 
            {{ $t('email.WhereYourTeachers') }}  
            {{ $t('email.AlsoWhereStartLiveStream') }}  

            <h3 v-bind:style="styleObjectH3">{{ $t('email.CheckinModule') }}: {{checkin_link}}</h3> 
            {{ $t('email.CanRunOnTablet') }}  

            <h3 v-bind:style="styleObjectH3">{{ $t('email.NativeApp') }}:</h3> 
            {{ $t('email.YogoNativeApp') }} 
        </div>
      </div>
     
      <div v-if="error" class="md:bg-white md:border rounded border-gray-200 py-8 md:py-10 sm:px-6 md:px-8">
        <h2 class="mt-6 text-center">
          <p v-if="errorType == 'expired'">{{ $t('login.linkExpired') }}</p>            
          <p v-if="errorType == 'invalid'">{{ $t('login.linkInvalid') }}</p>            
        </h2>
        
        <div style="width: 500px; margin: 40px 30px 20px; font-size: 20px">
          <p v-if="errorType == 'expired'">{{ $t('login.linkExpiredComment') }}</p>            
          <p v-if="errorType == 'invalid'">{{ $t('login.linkInvalidComment') }}</p>            
        </div>

        <button class="btn-primary mt-8 w-full" type="button" v-on:click="goHome">
          {{ $t('login.startMyFreeDemo') }}
        </button>
        
      </div>

      <div v-if="loading" class="md:bg-white md:border rounded border-gray-200 py-8 md:py-10 sm:px-6 md:px-8">
        <clip-loader :loading = "loading" :color = "color" :size = "size" ></clip-loader>
      </div>

      
    </div>

  </div>
</template>

<script>

import { mapGetters } from 'vuex';
import YogoApi from '@/gateways/YogoApi';
import ModalLogo from './ModalLogo';
import axios from 'axios';
import ClipLoader from 'vue-spinner/src/ClipLoader.vue'

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
      loading: false,

      first_name: '',
      admin_module_dashboard_link: '',
      frontend_my_profile_link: '',
      teacher_my_schedule_link: '',
      checkin_link: '',

      styleObjectH3: {
        wordBreak: 'break-all',
        marginTop: '20px',
      }
    }
  },
  components: {
    ModalLogo,
    ClipLoader,
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
        console.log("response = ", response);
        this.first_name = response.first_name
        this.admin_module_dashboard_link = response.admin_module_dashboard_link;
        this.frontend_my_profile_link = response.frontend_my_profile_link;
        this.teacher_my_schedule_link = response.teacher_my_schedule_link;
        this.checkin_link = response.checkin_link;

        console.log("first_name = ", this.first_name);
        console.log("admin_module_dashboard_link = ", this.admin_module_dashboard_link);
        console.log("teacher_my_schedule_link = ", this.teacher_my_schedule_link);
        console.log("teacher_my_schedule_link = ", this.teacher_my_schedule_link);
        console.log("checkin_link = ", this.checkin_link);

        this.loading = false;
        this.success = true;
        this.error = false;
      }
    }
  },
  methods: {
    goHome: function (event) {
      this.$router.push({name: 'Signup'}) 
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
