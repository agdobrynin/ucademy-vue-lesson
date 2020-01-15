<template lang="pug">
  div.container.pt-3#app
    h1.alert.alert-primary Hello World!
    form
        div.form-group
            label Email
            input(name="email" type="email" v-model="email"
                :class="{'is-invalid':$v.email.$invalid, 'is-valid':!$v.email.$invalid}"
                @input="$v.email.$touch()").form-control
            div(class="invalid-feedback" v-if="!$v.email.email") Please input correct email address
            div(class="invalid-feedback" v-if="!$v.email.required") Email is mandatory field

        div.form-group
            label Password
            input(name="password" type="password" v-model="password"
                :class="{'is-invalid':$v.password.$invalid, 'is-valid':!$v.password.$invalid}"
            @input="$v.password.$touch()").form-control
            div(class="invalid-feedback" v-if="!$v.password.required && !$v.password.$dirty") Password is mandatory field
            div(class="invalid-feedback" v-if="$v.password.$invalid && $v.password.$dirty")
                span
                    | Password must be more then #[strong {{ $v.password.$params.minLength.min }}] symbols.
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

        button(class="btn btn-primary" type="submit" :disabled="!canSubmit") Submit

    pre(v-if="isDebug") Debug validation: {{ $v }}
</template>

<script>

    import { required, email, minLength, sameAs } from "vuelidate/lib/validators";
    const MIN_PASSWORD_LENGTH = 6;

    export default {
        name: "app",
        data(){
            return {
                email: '',
                isDebug: false,
                password: '',
                confirmPassword: '',
            }
        },
        computed: {
            canSubmit() {
                const validator = this.$v;
                return !validator.email.$invalid && !validator.password.$invalid&& !validator.confirmPassword.$invalid;
            }
        },
        validations: {
            email: {
                required: required,
                email: email,
            },
            password: {
                minLength: minLength(MIN_PASSWORD_LENGTH),
                required: required,
            },
            confirmPassword: {
                required: required,
                confirmPassword: sameAs( (vue) => {
                    return vue.password;
                }),
            }
        }
    }
</script>

<style lang="scss">
    @import "~bootstrap/scss/bootstrap";
</style>
