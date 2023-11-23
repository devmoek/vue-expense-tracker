<template>
    <h4>Your Balance</h4>
    <strong id="balance">${{ total }}</strong>
</template>

<script setup>
const props = defineProps({
  total: {
    type: Number,
    required: true,
  },
});
</script>