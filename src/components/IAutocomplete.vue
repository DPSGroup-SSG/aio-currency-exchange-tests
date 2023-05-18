<script setup>
/**
 * Autocomplete component that allows searching for and selecting a value from the given list
 *
 * props:
 *     modelValue/v-model: Represents the key that has been selected.
 *     items: A JavaScript object where the keys represent the keys to be selected
 *         and the values represent their display name. Example:
 *         {
 *             eur: "Euro",
 *             usd: "United States Dollar",
 *             aud: "Australian Dollar",
 *             gbp: "Pound Sterling"
 *         }
 */
import {computed, ref} from "vue";

const props = defineProps(["modelValue", "items"])
const emit = defineEmits(["update:modelValue", "change"])

const keyValuePairs = computed(() => {
    return Object.keys(props.items).map(key => {
        return {
            key,
            value: props.items[key]
        }
    })
})

const textInput = ref("")
function onInput() {
    const value = props.items[textInput.value]
    if (value != null) {
        emit("update:modelValue", textInput.value);
        emit("change", {
            key: textInput.value,
            value
        });
    }
}

</script>

<template>
    <div>
        <input style="width: 100%" v-model="textInput" @input="onInput" list="autocomplete-list">
        <datalist id="autocomplete-list">
            <option v-for="item in keyValuePairs" :value="item.key">{{ item.value }}</option>
        </datalist>
    </div>
</template>

<style scoped>

</style>