<template>
  <div class="home">
    <b-container fluid>
      <b-row>
        <b-col cols="4" class="leftSection"></b-col>
        <b-col cols="4">
          <h2>Welcome, Please fill in the following details.</h2>

          <h5 v-if="errorMessage" class="text-danger">*{{ errorMessage }}</h5>


          <b-form @submit="joinQueue">
            <b-form-group
              id="input-group-1"
              label="*Name:"
              label-for="input-1"
            >
              <b-form-input
                id="input-1"
                v-model="clientName"
                type="text"
                placeholder="Enter your name"
                :disabled="isLoading"
                required
              ></b-form-input>
            </b-form-group>

            <br><br>

            <b-form-group
                v-slot="{ ariaDescribedby }"
                label="*Please select the service(s) you want to use today."> 
              <b-form-checkbox-group
                id="checkbox-group-1"
                v-model="chosenBankActions"
                :options="bankActions"
                :aria-describedby="ariaDescribedby"
                name="flavour-1"
                :disabled="isLoading"
              ></b-form-checkbox-group>
            </b-form-group>
            <b-button
              @click="joinQueue()"
              :disabled="isLoading"
              variant="outline-primary"
            >
              <b-spinner v-if="isLoading"></b-spinner>
              <template v-else>Button</template>
            </b-button>
          </b-form>
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'Home',
  data() {
    return {
      clientName: '',
      chosenBankActions: [],
      errorMessage: '',
      isLoading: false,
      bankActions: [
        {
          text: 'Cash Withdrawal',
          value: 'Cash Withdrawal',
        },
        {
          text: 'Cash Deposit',
          value: 'Cash Deposit',
        },
        {
          text: 'Cheque Deposit',
          value: 'Cheque Deposit',
        },
        {
          text: 'Cheque Withdrawal',
          value: 'Cheque Withdrawal',
        },
        {
          text: 'Currency Exchange',
          value: 'Currency Exchange',
        },
      ],
    }
  },
  methods: {
    async joinQueue() {
      this.isLoading = true;
      this.errorMessage = '';
      if (!this.clientName || !this.chosenBankActions.length) {
        this.errorMessage = 'Please fill in all the fields'
        this.isLoading = false;
        return;
      }
      const data = {
        name: this.clientName,
        bankActions: this.chosenBankActions,
      }

      const response = await axios.post('http://localhost:3000/', data);
      if (response.data.message !== 'Done') {
        this.errorMessage = response.data.message;
      } else {
        this.errorMessage = `Your number is ${response.data.client.pk}. Thank you`;
      }

      setTimeout(() => {
        this.clientName = '';
        this.chosenBankActions = [];
        this.errorMessage = '';
        this.isLoading = false;
      }, 3000);
    },
  },
}
</script>
