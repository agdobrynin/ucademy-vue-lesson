<template lang="pug">
  div.container.pt-3#app
    h1.alert.alert-primary Hello World!
    div.form-group
        label Email
        input(
            name="email"
            type="email"
            :value="email").form-control
</template>

<script>
    export default {
        name: "app",
        data(){
            return {
                email: "email@yahoo.com",
            }
        },
    }
</script>

<style lang="scss">
    @import "~bootstrap/scss/bootstrap";
</style>
