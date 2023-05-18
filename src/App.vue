<script setup>
import IAutocomplete from "./components/IAutocomplete.vue"
import {computed, onMounted, ref} from "vue";
import axios from "axios";

/*
 Gets a list of all the available currencies. Returns data in the form of:
 data: {
    aud: "Australian Dollar",
    eur: "Euro",
    gbp: "Pound Sterling"
    usd: "United States Dollar",
 }
 */
const allCurrenciesUrl = "https://cdn.jsdelivr.net/gh/fawazahmed0/currency-api@1/latest/currencies.json";

/*
 Gets a list of all exchange rates for the given currency. Returns data in the form of:
 data: {
    "date": "2023-02-01",
    "eur": {
        "aud": 1.53988,
        "usd": 1.086365,
        "gbp": 0.882308
    }
 }
 */
const currencyRatesUrl = (currency) => `https://cdn.jsdelivr.net/gh/fawazahmed0/currency-api@1/latest/currencies/${currency}.json`

const items = ref({})
const currencyData = ref({})
const model = ref("")
const currencyValue = ref(1)

onMounted(() => {
    axios.get(allCurrenciesUrl).then((result) => {
        items.value = result.data
    })
})

function onCurrencySelected(currency) {
    axios.get(currencyRatesUrl(currency.key)).then((result) => {
        currencyData.value = result.data
    })
}

const currencyRates = computed(() => {
    const currencyObj = currencyData.value[model.value] ?? {}
    return Object.keys(currencyObj).map(key => {
        return {
            key,
            value: currencyObj[key]
        }
    })
})

function getCurrencyDisplayTest(key) {
    if (key == null) return "";
    return `${items.value[key]} (${key.toUpperCase()})`;
}

const currentCurrency = computed(() => {
    return getCurrencyDisplayTest(model.value)
})

function evaluateCurrencyExchange(currencyRate) {
    const value = currencyRate * currencyValue.value
    return value.toLocaleString("en", {maximumFractionDigits: 2})
}

</script>

<template>
    <main class="main-content">
        <h1>Currency Converter</h1>
        <div class="input-fields">
            <label>Currency</label>
            <label>Amount</label>
            <i-autocomplete class="currency-select" v-model="model" :items="items" @change="onCurrencySelected"></i-autocomplete>
            <input v-model="currencyValue" type="number">
        </div>
        <div v-if="model.length > 0" class="currency-rates-container">
            <h2>{{ currencyValue }} {{currentCurrency}}</h2>
            <div class="currency-table">
                <template v-for="rate in currencyRates">
                    <span class="value-cell">{{ evaluateCurrencyExchange(rate.value) }}</span>
                    <span class="currency-cell">{{ getCurrencyDisplayTest(rate.key) }}</span>
                </template>
            </div>
        </div>
    </main>
</template>

<style scoped>
.main-content {
    width: clamp(400px, 50%, 900px);
    margin: 2rem auto auto;
    padding: 2rem;
    display: flex;
    gap: 0.5rem;
    flex-direction: column;
    border-radius: 1rem;
    box-shadow: 0 10px 20px grey;
    background-color: white;
}

.input-fields {
    display: grid;
    grid-template-columns: 1fr 1fr;
    column-gap: 2rem;
    margin-bottom: 1rem;
}

.currency-rates-container {
    display: flex;
    flex-direction: column;
    gap: 1rem;
    margin-block: 1rem;
}

.currency-select :deep(input) {
    text-transform: uppercase;
}

.currency-table {
    display: grid;
    grid-template-columns: 1fr 2fr;
    column-gap: 1rem;
}

.value-cell {
    margin-left: auto;
}
</style>
