<template>
  <div
    class="min-h-screen flex items-center justify-center md:bg-gray-50 py-12 px-4 sm:px-6 lg:px-8">
    <div class="max-w-md w-full">

      <div class="md:bg-white md:border rounded border-gray-200 py-8 md:py-10 sm:px-6 md:px-8">
        <ModalLogo/>
        <h2 class="mt-6 text-center">
          <p>{{ $t('login.clientSignupNotification') }}</p>            
        </h2>
        
        <div style="width: 350px; text-align: center; margin: auto; margin-top: 40px; font-size: 20px">
            <p>{{ $t('login.hiYogo') }}.</p>
            <p>{{ $t('login.signedUpDemo') }}:</p>
            <p>{{ $t('global.ID') }}: {{id}}</p>
            <p>{{ $t('global.StudioName') }}: {{client_name}}</p>
            <p>{{ $t('login.UserName') }}: {{username}}</p>
            <p>{{ $t('global.Email') }}: {{email}}</p>
        </div>
      </div>
    </div>

  </div>
</template>

<script>

import { validationMixin } from 'vuelidate';
import { required, email, minLength, sameAs } from 'vuelidate/lib/validators'
import { mapGetters } from 'vuex';
import YogoApi from '@/gateways/YogoApi';
import Imgix from '@/services/Imgix';
import InputField from './InputField.vue';
import Modal from './Modal.vue';
import ModalLogo from './ModalLogo';
import axios from 'axios';

export default {
  mixins: [validationMixin, Imgix],
  components: {
    ModalLogo,
    InputField,
    Modal,
  },
  data() {
    return {
      // client_name: '',
      // first_name: '',
      // last_name: '',
      // email: '',
      // password: '',
      rememberMe: false,
      loginErrorMessage: '',
      loginFailed: false,
      form: {
        client_name: '',
        first_name: '',
        last_name: '',
        email: '',
        password: '',
        confirm_password: '',
        locale: this.$route.query.local? this.$route.query.local: "en",
      },

    };
  },
  computed: {
    ...mapGetters([
      'requestedRoute',
      'imageServer',
      'client',
    ])
  },
  methods: {

    getValidationClass(fieldName) {
      const field = this.$v.form[fieldName];

      if (field) {
        return {
          invalid: field.$invalid && field.$dirty,
        };
      }
      return {};
    },

    async submitForm() {
      this.$v.$touch();
      
      console.log("form = ", this.form);

      if (this.$v.$invalid) return;

      // this.form["locale"] = local;
      console.log("form = ", this.form);

      const response = await YogoApi.post('/client-signing-up', this.form);

      if (response.error) {
        console.log("response.error = ", response.error);
        console.log("response.message = ", response.message);
      } else {
        console.log("response = ", response);

      }
  
      // if (response.error) {
      //   this.$store.commit('showAlert', {
      //     title: response.error.localized_title,
      //     message: response.error.localized_message,
      //   });
      //   return;
      // }

      // if (!response.user || !response.token) {
      //   alert(this.$t('login.couldNotLogIn'));
      //   return;
      // }

      // if (this.rememberMe) {
      //   window.localStorage.setItem('accessToken', response.token);
      //   window.sessionStorage.removeItem('accessToken');
      // } else {
      //   window.localStorage.removeItem('accessToken');
      //   window.sessionStorage.setItem('accessToken', response.token);
      // }
      // await this.$store.dispatch('updateUser', response.user.id);
      // if (this.requestedRoute) {
      //   const requestedRoute = _.pick(this.requestedRoute, ['name', 'params']);
      //   this.$store.commit('setRequestedRoute', null);
      //   this.$router.push(requestedRoute);
      // } else {
      //   this.$router.push({ name: 'MySchedule' });
      // }
    },
  },
  validations: {
    form: {
      client_name: {
        required,
      },
      first_name: {
        required,
      },
      last_name: {
        required,
      },
      email: {
        required,
        email,
      },
      password: {
        required,
        valid: function(value) {
          const containsUppercase = /[A-Z]/.test(value)
          const containsLowercase = /[a-z]/.test(value)
          const containsNumber = /[0-9]/.test(value)
          // const containsSpecial = /[#?!@$%^&*-]/.test(value)
          return containsUppercase && containsLowercase && containsNumber
        },
        minLength: minLength(6),
        // maxLength: maxLength(19),
      },
      confirm_password: { 
        required, 
        sameAsPassword: sameAs("password") 
      },
      locale: {},
    },
  },
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
