<template lang="pug">
  div.container.pt-3#app
    h1.alert.alert-primary Hello World!
    form(@submit.prevent="" method="post")
      div.form-group
        label Email #[span(v-if="!isEmailUnique") is not unique, please try other address]
        input(name="email" type="email"
          v-model="email"
          :class="{'is-invalid':!isValidEmail, 'is-valid':isValidEmail}"
          @input="validateEmail"
        ).form-control
        div(class="invalid-feedback" v-if="!$v.email.email") Please input correct email address
        div(class="invalid-feedback" v-if="!$v.email.required") Email is mandatory field

      div.form-group
        label Password
        input(name="password" type="password" v-model="password"
          :class="{'is-invalid':$v.password.$invalid, 'is-valid':!$v.password.$invalid}"
          @input="$v.password.$touch()").form-control
        div(class="invalid-feedback" v-if="!$v.password.required && !$v.password.$dirty") Password is mandatory field
        div(class="invalid-feedback" v-if="$v.password.$invalid && $v.password.$dirty ")
          span
            | #[span(v-if="password.length < $v.password.$params.minLength.min") Password must be more then #[strong {{ $v.password.$params.minLength.min }}] symbols.]
            | #[span(v-if="!$v.password.strongPasswordWithNumbers") Password must content {{PASSWORD_STRONG_WITH_NUMBER_MIN}} numbers]
            | Now password is #[strong {{ password.length }}] symbols.

      div.form-group
        label Confirm password
        input(name="confirmPassword" type="password" v-model="confirmPassword"
          :class="{'is-invalid':$v.confirmPassword.$invalid, 'is-valid':!$v.confirmPassword.$invalid}"
          @input="$v.confirmPassword.$touch()").form-control
        div(class="invalid-feedback" v-if="!$v.confirmPassword.required && !$v.confirmPassword.$dirty") Confirm password
        div(class="invalid-feedback" v-if="$v.confirmPassword.$invalid && $v.confirmPassword.$dirty") Password not matched

      div.form-check.form-group
        input(v-model="isDebug" type="checkbox" id="checkDebug")
        label(for="checkDebug") &nbsp;Debug validation info

      input(class="btn btn-primary" type="submit" value="Register" name="register"
        :disabled="!canSubmit"
        @click="checkEmailAsUnique")

    div.alert.alert-danger.alert-dismissible.fade.show.mt-3(v-if="checkService.fail" role="alert")
      h5 Axios request #[hr]
      p {{ checkService.message }}
      button(type="button" class="close" @click="checkService.fail = false")
        span(aria-hidden="true") &times;


    pre(v-if="isDebug") Debug validation: {{ $v }}
</template>

<script>

  import {required, email, minLength, sameAs} from "vuelidate/lib/validators";
  import axios from "axios";

  const MIN_PASSWORD_LENGTH = 6;
  const PASSWORD_STRONG_WITH_NUMBER_MIN = 2;
  const URL_CHECK_UNIQUE_EMAIL = "back.json";

  export default {
    name: "app",
    created() {
      this.PASSWORD_STRONG_WITH_NUMBER_MIN = PASSWORD_STRONG_WITH_NUMBER_MIN;
    },
    data() {
      return {
        email: 'as@as.cc',
        isDebug: false,
        password: '',
        confirmPassword: '',
        isEmailUnique: true,
        checkService: {
          fail: false,
          message: '',
        }
      }
    },
    methods: {

      checkEmailAsUnique: async function (event) {
        this.checkService.fail = false;
        try {
          let {data} = await axios.get(URL_CHECK_UNIQUE_EMAIL, {params: {'email': this.email}});
          this.isEmailUnique = !(data.email.toLocaleLowerCase() === this.email.toLocaleLowerCase());
          if (this.isEmailUnique) {
            event.target.form.submit();
            return;
          }
          throw new ReferenceError('Email is not unique');
        } catch (error) {
          this.checkService.fail = true;
          this.checkService.message = error.message;
        }
      },

      validateEmail() {
        this.$v.email.$touch();
        this.isEmailUnique = true;
      },
    },
    computed: {
      canSubmit() {
        return !this.$v.$invalid && this.isEmailUnique;
      },
      isValidEmail() {
        return !this.$v.email.$invalid && this.isEmailUnique;
      },
    },
    validations: {
      email: {
        required: required,
        email: email,
      },
      password: {
        minLength: minLength(MIN_PASSWORD_LENGTH),
        required: required,
        strongPasswordWithNumbers: (newValue) => {
          const numberMatches = newValue.match(/\d+/g) || [];
          return numberMatches.join('').length >= PASSWORD_STRONG_WITH_NUMBER_MIN;
        },
      },
      confirmPassword: {
        required: required,
        confirmPassword: sameAs((vue) => {
          return vue.password;
        }),
      }
    }
  }
</script>

<style lang="scss">
  @import "~bootstrap/scss/bootstrap";
</style>
