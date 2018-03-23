<template>
  <div id="app">
    <link href='https://fonts.googleapis.com/css?family=Roboto:300,400,500,700|Material+Icons' rel="stylesheet">
    <v-app>
      <payment-form stripe-key="stripePublicKey" :payment-methods="paymentMethods"></payment-form>
    </v-app>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import PaymentForm from './components/PaymentForm.vue';
import 'vuetify/dist/vuetify.min.css';

interface PaymentMethod { name: string; token: string; }

@Component({
  components: {
    PaymentForm,
  },
})
export default class App extends Vue {
  private customerToken: string = '';
  private stripePublicKey: string = '';
  private paymentMethods: PaymentMethod[] = [
    { name: 'STRIPE TEST BANK', token: 'ba_1C28nvLKiCQA0SUAoCR8MOwj' }
  ]
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
