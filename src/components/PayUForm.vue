<template>
	<v-container>
		<v-row class="text-center">
			<v-col cols="12">
				<h1>PayU Test</h1>
			</v-col>
      <v-col cols="12" v-if="showPaymentButton">
        <form method="post" :action="payuGateway">
          <input name="merchantId"          type="hidden"  :value="merchantId"   >
          <input name="accountId"           type="hidden"  :value="accountId" >
          <input name="description"         type="hidden"  :value="description"  >
          <input name="referenceCode"       type="hidden"  :value="referenceCode" >
          <input name="amount"              type="hidden"  :value="amount"   >
          <input name="tax"                 type="hidden"  :value="tax"  >
          <input name="taxReturnBase"       type="hidden"  :value="taxReturnBase" >
          <input name="currency"            type="hidden"  :value="currency" >
          <input name="signature"           type="hidden"  :value="signature"  >
          <input name="test"                type="hidden"  :value="isTest" >
          <input name="buyerEmail"          type="hidden"  :value="buyerEmail" >
          <input name="responseUrl"         type="hidden"  :value="responseUrl" >
          <input name="confirmationUrl"     type="hidden"  :value="confirmationUrl" >
          <input name="algorithmSignature"  type="hidden"  value="SHA256" >
          <input name="Submit"              type="submit"  value="Proceed to PayU" >
        </form>
      </v-col>
      <v-col cols="12" v-if="!showPaymentButton">
        <v-btn @click="sendRequest">
          Pay with PayU
        </v-btn>
      </v-col>
		</v-row>
	</v-container>
</template>

<script>
import axios from "axios";
const baseURL = process.env.VUE_APP_BASE_URL_API;
const merchantId = process.env.VUE_APP_MERCHAND_ID;
const accountId = process.env.VUE_APP_ACCOUNT_ID;

export default {
	name: "PayUForm",
	data: () => ({ 
    payuGateway:"https://sandbox.checkout.payulatam.com/ppp-web-gateway-payu/",
    merchantId:"",
    accountId:"",
    description:"",
    referenceCode:"",
    amount:null,
    tax:null,
    taxReturnBase:null,
    currency:"",
    signature:"",
    isTest:1,
    buyerEmail:"",
    responseUrl:"",
    confirmationUrl:"",
    showPaymentButton:false,
	}),
  mounted(){
    let prductDescription = "Product description";
    let payuMinimumAmount = 15000;
    let localTax = 0.19;
    let localCurrency = 'COP';
    let isBeingTested = 1;
    let buyerEmail = "test@test.com";
    let responseUrl = window.location.href;

    this.merchantId = merchantId;
    this.accountId = accountId;
    this.description = prductDescription;
    this.amount = payuMinimumAmount ;
    this.currency = localCurrency;
    this.tax = Math.round((this.amount/(1+localTax))*localTax);
    this.taxReturnBase = Math.round(this.amount/(1+localTax));
    this.isTest = isBeingTested;
    this.buyerEmail = buyerEmail;
    this.responseUrl = responseUrl;
    this.confirmationUrl = `${baseURL}api/Payu/confirmation`;
  },
	methods:{
		sendRequest(){
			axios.get(`${baseURL}api/Payu/payment-signature?amount=${this.amount}&currency=${this.currency}`)
				.then(res => {
          console.log(res)
          if(res.status == 200){
            this.referenceCode = res.data.referenceCode
            this.signature = res.data.signature
            this.showPaymentButton = true;
          }
				})
				.catch(error => { 
					console.log(error);
				});
		}
	}
};
</script>
