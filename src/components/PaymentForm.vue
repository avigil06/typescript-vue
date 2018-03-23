<template>
  <div class="payment-form">
    <v-form>
      <v-stepper v-model="currentStep" vertical>

        <v-stepper-step step="1" :complete="currentStep > 1">Add a donation</v-stepper-step>
        <v-stepper-content step="1">
          <v-container v-if="lineItems.length">
            <v-layout row v-for="lineItem in lineItems">
              <v-flex class="line-item" xs8>{{ lineItem.category }}</v-flex>
              <v-flex class="line-amount" xs4>${{ lineItem.amount }}</v-flex>
            </v-layout>
            <v-divider></v-divider>
            <v-layout row>
              <v-flex class="line-item" xs8>Total</v-flex>
              <v-flex class="line-amount" xs4>${{ total }}</v-flex>
            </v-layout>
          </v-container>

          <v-container fluid>
            <v-text-field
              name="amount"
              label="Amount"
              prefix="$"
              type="number"
              v-model.number.trim="amount"
              @input="trimZeros"
              @blur="setDecimal"
              class="input-group-focused"></v-text-field>
            <v-select
              name="category"
              label="Donate To"
              :items="categories"
              v-model="category"></v-select>
            <v-text-field v-if="category=='Other'"
              name="other"
              label="Other"
              v-model="categoryOther"></v-text-field>
          </v-container>

          <v-container fluid>
            <v-layout row wrap>
              <v-flex xs12><v-btn @click="addToDonation">Add to Donation</v-btn></v-flex>
              <v-flex xs12><v-btn xs12 v-show="lineItems.length" @click.native="currentStep=2">Payment Methods</v-btn></v-flex>
            </v-layout>
          </v-container>
        </v-stepper-content>

        <v-stepper-step step="2" :complete="currentStep > 2">Select a payment method</v-stepper-step>
        <v-stepper-content step="2">
          <v-container v-if="lineItems.length">
            <v-layout row v-for="lineItem in lineItems">
              <v-flex class="line-item" xs8>{{ lineItem.category }}</v-flex>
              <v-flex class="line-amount" xs4>${{ lineItem.amount }}</v-flex>
            </v-layout>
            <v-divider></v-divider>
            <v-layout row>
              <v-flex class="line-item" xs8>Total</v-flex>
              <v-flex class="line-amount" xs4>${{ total }}</v-flex>
            </v-layout>
          </v-container>

          <v-container fluid>
            <v-select
              name="paymentMethod"
              label="Payment Method"
              :items="paymentMethods"
              item-value="token"
              item-text="name"
              v-model="paymentMethod"></v-select>
          </v-container>

          <v-container fluid>
            <v-btn @click="currentStep=3">Summary</v-btn>
          </v-container>
        </v-stepper-content>

        <v-stepper-step step="3" :complete="currentStep > 3">Authorize your donation</v-stepper-step>
        <v-stepper-content step="3">

          <v-container fluid>
            I authorize Stallings United Methodist Church to electronically debit my account and, if necessary, electronically credit my account to correct erroneous debits.
          </v-container>

          <v-container fluid>
            <v-btn @click="">Authorize Charge</v-btn>
          </v-container>
        </v-stepper-content>
      </v-stepper>
    </v-form>

    <v-snackbar
      :timeout="2500"
      color="error"
      v-model="snackbar">{{ error }}</v-snackbar>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import { sum, map } from 'ramda';

interface LineItem { amount: number; category: string; }
interface PaymentMethod { name: string; token: string; }

@Component
export default class PaymentForm extends Vue {

  private categories: string[] = ['General Operations', 'Building Fund', 'Preschool Tuition', '20/20 Vision', 'Other'];

  private currentStep: number = 1;
  private amount: any = 0;
  private category: string = '';
  private categoryOther: string = '';
  private lineItems: LineItem[] = [];
  private snackbar: boolean = false;
  private error: string = '';
  private paymentMethod?: PaymentMethod;

  @Prop() private stripeKey!: string;
  @Prop() paymentMethods: PaymentMethod[];

  private created() {
    this.paymentMethod = this.paymentMethods[0];
  }

  private get total() {
    const mapFloat = (lineItem: LineItem) => lineItem.amount;
    return sum(map(mapFloat, this.lineItems)).toFixed(2);
  }


  private trimZeros(value: string) {
    this.amount = parseFloat(value);
  }

  private setDecimal()  {
    this.amount = this.amount.toFixed(2);
  }

  private addToDonation() {
    const { amount, category, categoryOther } = this;
    const lineItem: LineItem = { amount, category: category === 'Other' ? categoryOther : category };

    if (!lineItem.amount || !lineItem.category) {
      return this.setSnackBar('Please provide a donation amount and category.');
    }

    this.lineItems.push(lineItem);
    this.amount = 0;
    this.category = '';
  }

  private resetSnackBar() {
    this.snackbar = false;
    this.error = '';
  }

  private setSnackBar(error: string) {
    this.resetSnackBar();
    this.error = error;
    this.snackbar = true;
  }


}
</script>

<style scoped lang="scss">
.line {
  &-item {
    text-align: right;
  }

  &-amount {
    font-weight: bold;
  }
}
</style>
