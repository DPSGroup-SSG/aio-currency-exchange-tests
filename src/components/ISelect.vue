<script setup>
defineProps(["modelValue", "items"])
defineEmits(["update:modelValue"])

</script>

<template>
    <select :value="modelValue" @input="$emit('update:modelValue', $event.target.value)">
        <option v-for="item in items" :value="item">{{ item }}</option>
    </select>
</template>

<style scoped>

</style>