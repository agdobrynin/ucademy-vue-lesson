<template lang="pug">
  div.container.pt-3#app
    h1.alert.alert-primary Hello World!
    form
        div.form-group
            label Email
            input(
                name="email"
                type="email"
                v-model="email"
                :class="{'is-invalid':$v.email.$invalid}"
                @input="$v.email.$touch()").form-control
            div(
                class="invalid-feedback"
                v-if="!$v.email.email") Please input correct email address
            div(
                class="invalid-feedback"
                v-if="!$v.email.required") Email is mandatory field

        div.form-check.form-group
            input(
                v-model="isDebug"
                type="checkbox"
                id="checkDebug")
            label(
                for="checkDebug"
            ) &nbsp;Debug validation info

    pre(v-if="isDebug") Debug validation: {{ $v }}
</template>

<script>

    import { required, email } from "vuelidate/lib/validators";

    export default {
        name: "app",
        data(){
            return {
                email: '',
                isDebug: false,
            }
        },
        validations: {
            email: {
                required: required,
                email: email,
            }
        }
    }
</script>

<style lang="scss">
    @import "~bootstrap/scss/bootstrap";
</style>
