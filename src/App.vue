<template lang="pug">
  div.container.pt-3#app
    h1.alert.alert-primary Hello World!
    div.form-group
        label Email
        input(
            name="email"
            type="email"
            v-model="email"
            @input="$v.email.$touch()").form-control
    pre {{ $v }}
</template>

<script>
    
    import { required, email } from "vuelidate/lib/validators";

    export default {
        name: "app",
        data(){
            return {
                email: '',
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
