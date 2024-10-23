<template>
  <div>
    <div v-if="Object.keys(prices).length > 0">
      <div v-for="(price, symbol) in prices" :key="symbol">
        {{ symbol }} Price: ${{ price.toFixed(2) }}
      </div>
    </div>
    <div v-else>
      Loading prices...
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const API_KEY = process.env.VUE_APP_CMC_API_KEY;

const prices = ref({});

const fetchCryptoPrices = async () => {
  try {
    const response = await axios.get('https://pro-api.coinmarketcap.com/v1/cryptocurrency/listings/latest', {
      headers: {
        'X-CMC_PRO_API_KEY': API_KEY,
        'Accept': 'application/json'
      },
      params: {
        start: 1,
        limit: 10,
        convert: 'USD'
      }
    });

    const coins = response.data.data;

    ['BTC', 'ETH', 'SOL', 'ADA'].forEach(symbol => {
      const coin = coins.find(coin => coin.symbol === symbol);
      if (coin) {
        prices.value[symbol] = coin.quote.USD.price;
      } else {
        console.log(`${symbol} data not found.`);
      }
    });
  } catch (error) {
    console.error('Error fetching data from CoinMarketCap:', error.response?.data?.status?.error_message || error.message);
  }
};

console.log('API Key:', API_KEY);

onMounted(() => {
  fetchCryptoPrices();
});
</script>
