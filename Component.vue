<script setup>
  import { ref, computed } from 'vue'

  const currencies = ref({});
  const to_eur = ref({});
  const from = ref('usd');
  const to = ref('kes');
  const amount = ref(1);

  // Fetch the list of supported currencies.
  fetch('https://cdn.jsdelivr.net/gh/fawazahmed0/currency-api@1/latest/currencies.min.json')
    .then( response => response.json() )
    .then( data => {
      currencies.value = data;
      return data;
    } );

  // Fetch the conversion rate to EUR.
  fetch('https://cdn.jsdelivr.net/gh/fawazahmed0/currency-api@1/latest/currencies/eur.min.json')
      .then( response => response.json() )
      .then( data => data.eur )
      .then( data => {
        to_eur.value = data;
        return data;
      } );
 
  // Converts from 1 currency to another.
  function Convert( amount, from, to ) {
    
    // Prepare conversion rates.
    const from_to_eur = to_eur.value[from] ? to_eur.value[from] : 1;
    const eur_to_final = to_eur.value[to] ? to_eur.value[to] : 1;

    // Convert to EUR then to final currency. 
    const value = amount / from_to_eur * eur_to_final;
    return value.toFixed(2);
  }

   // Compute the final amount.
   const finalAmount = computed(() => Convert( amount.value, from.value, to.value ) );
  
   // Compute full from
   const fullFrom = computed(() => currencies.value[from.value] ? currencies.value[from.value] : from.value );
  
  // Compute full to
   const fullTo = computed(() => currencies.value[to.value] ? currencies.value[to.value] : to.value );
</script>

<template>
  <div class="wrapper">
      <h1>Currency Converter</h1>

      <p>
        <label>
          <span>Amount</span>
          <input v-model="amount" />
        </label>
      </p>

      <p>
        <label>
            <span>Convert From</span>
            <select v-model="from">
                <option v-for="(currency, key) in currencies" :value="key">{{ currency }}</option>
            </select>
          </label>
      </p>

      <p>
        <label>
            <span>Convert To</span>
            <select v-model="to">
                <option v-for="(currency, key) in currencies" :value="key">{{ currency }}</option>
            </select>
          </label>
      </p>

        <h2>{{ amount }} {{from}} = {{ finalAmount }} {{ to }}</h2>
  </div>
</template>

<style>
  @import url('https://fonts.googleapis.com/css?family=Quicksand&display=swap');

  * {
    box-sizing: border-box;
  }
 
  body {
    margin: 0;
    background: #f1f1f1;
    display: flex;
    align-items: center;
    justify-content: center;
    min-height: 90vh;
    font-family: Quicksand;
  }

  .wrapper {
    background: #fff;
    padding: 30px;
    border-radius: 10px;
  }

  input {
    display: block;
    width: 100%;
    padding: 0.375rem 0.75rem;
    font-size: 1rem;
    font-weight: 400;
    line-height: 1.5;
    color: #212529;
    background-color: #fff;
    background-clip: padding-box;
    border: 1px solid #ced4da;
    margin: 0;
    font-family: inherit;
    border-radius: 0.25rem;
    transition: border-color .15s ease-in-out,box-shadow .15s ease-in-out;
  }
  
  select {
    display: block;
    width: 100%;
    padding: 0.375rem 2.25rem 0.375rem 0.75rem;
    -moz-padding-start: calc(0.75rem - 3px);
    font-size: 1rem;
    font-weight: 400;
    line-height: 1.5;
    color: #212529;
    background-color: #fff;
    background-repeat: no-repeat;
    background-position: right 0.75rem center;
    background-size: 16px 12px;
    border: 1px solid #ced4da;
    border-radius: 0.25rem;
    transition: border-color .15s ease-in-out,box-shadow .15s ease-in-out;
  }

  h2 {
    color: #616161;
    font-size: 1.1rem;
  }
  
  p {
    margin-bottom: 2rem;
  }
</style>
