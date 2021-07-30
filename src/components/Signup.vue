<template>
  <div
    class="min-h-screen flex items-center justify-center md:bg-gray-50 py-12 px-4 sm:px-6 lg:px-8">
    <div class="max-w-md w-full">

      <div class="md:bg-white md:border rounded border-gray-200 py-8 md:py-10 sm:px-6 md:px-8">
        <ModalLogo/>
        <h2 class="mt-6 text-center">
            <p>{{ $t('login.welcomeToYogo') }}</p>
            <p style="width: 300px; text-align: center; margin: auto; margin-top: 20px;">{{ $t('login.letsStartFreeDemo') }}</p>
        </h2>

        <div class="message" v-show="loginErrorMessage">
          {{ loginErrorMessage }}
        </div>

        <form class="mt-8" novalidate @submit.prevent="submitForm">
            <input type="hidden" v-model="form.locale">
            <InputField
                :label="$t('global.StudioName')"
                :class="getValidationClass('client_name')"
                type="text"
                v-model="form.client_name"
            >
                <template v-slot:errors>
                  <span class="error" v-if="!$v.form.client_name.required">
                      {{ $t('login.studioNameMustBeEntered') }}
                  </span>
                </template>
            </InputField>

            <InputField
                :label="$t('global.FirstName')"
                :class="getValidationClass('first_name')"
                type="text"
                v-model="form.first_name"
            >
                <template v-slot:errors>
                  <span class="error" v-if="!$v.form.first_name.required">
                      {{ $t('login.firstNameMustBeEntered') }}
                  </span>
                </template>
            </InputField>

            <InputField
                :label="$t('global.LastName')"
                :class="getValidationClass('last_name')"
                type="text"
                v-model="form.last_name"
            >
                <template v-slot:errors>
                  <span class="error" v-if="!$v.form.last_name.required">
                      {{ $t('login.lastNameMustBeEntered') }}
                  </span>
                </template>
            </InputField>

            <InputField
                :label="$t('global.Email')"
                :class="getValidationClass('email')"
                type="email"
                v-model="form.email"
                autocomplete="email"
            >
                <template v-slot:errors>
                  <span class="error" v-if="!$v.form.email.required">
                      {{ $t('login.emailMustBeEntered') }}
                  </span>
                  <span class="error"
                      v-else-if="!$v.form.email.email">{{ $t('login.notValidEmail') }}</span>
                </template>
            </InputField>

            <InputField
                :label="$t('global.Password')"
                :class="getValidationClass('password')"
                type="password"
                v-model="form.password"
            >
                <template v-slot:errors>
                  <span class="error" v-if="!$v.form.password.valid || !$v.form.password.minLength">{{ $t('login.passDesc') }}</span>
                </template>
            </InputField>

            <!-- <InputField
                :label="$t('global.passwordConfirm')"
                :class="getValidationClass('confirm_password')"
                type="password"
                v-model="form.confirm_password"
            >
                <template v-slot:errors>
                  <span class="error" v-if="!$v.form.confirm_password.required">
                      {{ $t('login.passwordMustBeRepeated') }}
                  </span>
                  <span class="error" v-if="!$v.form.confirm_password.sameAsPassword">{{ $t('login.passwordIsDifferent') }}</span>
                </template>
            </InputField> -->

            <button class="btn-primary mt-8 w-full d-flex" type="submit" v-bind:style="{display: 'flex', justifyContent: 'center'}">
                {{ $t('login.startMyFreeDemo') }} 
                <clip-loader :loading="loading" :color="color" :size="size" v-bind:style="{marginLeft: '20px'}"></clip-loader>
            </button>

        </form>

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
import ClipLoader from 'vue-spinner/src/ClipLoader.vue'


export default {
  mixins: [validationMixin, Imgix],
  components: {
    ModalLogo,
    InputField,
    Modal,
    ClipLoader,
  },
  data() {
    return {
      rememberMe: false,
      loginErrorMessage: '',
      loginFailed: false,
      form: {
        client_name: '',
        first_name: '',
        last_name: '',
        email: '',
        password: '',
        locale: this.$route.query.locale? this.$route.query.locale: "en",
      },

      color: '#ffffff',
      size: '15px',
      margin: '2px',
      radius: '2px',
      loading: false

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
      
      console.log("form = ", this.form, this.$route.query);


      if (this.$v.$invalid) return;

      this.loading = true;

      const response = await YogoApi.post('/client-signing-up?from=' + window.location.origin, this.form);

      if (response.error) {
        this.loading = false;
        this.$notify({
          group: 'foo',
          title: 'Error',
          text: response.error,
          type: 'error',
        });
      } else {
        this.loading = false;
        this.$router.push({
          name: 'FirstStepConfirm',
          params: { },
        });

      }
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
      },
      // confirm_password: { 
      //   required, 
      //   sameAsPassword: sameAs("password") 
      // },
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
