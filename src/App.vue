<template lang="pug">
  div.container.pt-3#app
    h1.alert.alert-primary Hello World!
    form
        div.form-group
            label Email
            input(name="email" type="email" v-model="email" :class="{'is-invalid':$v.email.$invalid}"
                @input="$v.email.$touch()").form-control
            div(class="invalid-feedback" v-if="!$v.email.email") Please input correct email address
            div(class="invalid-feedback" v-if="!$v.email.required") Email is mandatory field

        div.form-group
            label Password
            input(name="password" type="password" v-model="password" :class="{'is-invalid':$v.password.$invalid}"
            @input="$v.password.$touch()").form-control
            div(class="invalid-feedback" v-if="!$v.password.required && !$v.password.$dirty") Password is mandatory field
            div(class="invalid-feedback" v-if="password.length < 6 && $v.password.$dirty")
                span
                    | Password must be more then {{ $v.password.$params.minLength.min }} symbols.
                    | Now password is #[strong {{ password.length }}] symbols.

        div.form-check.form-group
            input(v-model="isDebug" type="checkbox" id="checkDebug")
            label(for="checkDebug") &nbsp;Debug validation info

    pre(v-if="isDebug") Debug validation: {{ $v }}
</template>

<script>

    import { required, email, minLength } from "vuelidate/lib/validators";

    export default {
        name: "app",
        data(){
            return {
                email: '',
                isDebug: false,
                password: '',
            }
        },
        validations: {
            email: {
                required: required,
                email: email,
            },
            password: {
                minLength: minLength(6),
                required: required,
            },
        }
    }
</script>

<style lang="scss">
    @import "~bootstrap/scss/bootstrap";
</style>
