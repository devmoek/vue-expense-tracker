<template>
  <h3>Add new transaction</h3>
  <form id="form" @submit.prevent="onSubmit">
    <div class="form-control">
      <label for="text">Text</label>
      <input
        type="text"
        id="text"
        v-model="text"
        placeholder="Enter text..."
        @input="validateText"
      />
    </div>
    <div class="form-control">
      <label for="amount">Amount </label>
      <small> (negative - expense, positive - income)</small>
      <input
        type="text"
        id="amount"
        v-model="amount"
        placeholder="Enter amount..."
        @input="validateAmount"
      />
    </div>
    <button class="btn" :class="{'disabled-btn' : isButtonDisabled}" :disabled="isButtonDisabled">Add transaction</button>
  </form>
</template>

<script setup>
import { ref, computed } from "vue";
import { useToast } from "vue-toastification";

const text = ref("");
const amount = ref("");

const emit = defineEmits(["transactionSubmitted"]);

const toast = useToast();

const onSubmit = () => {
  if (!text.value || !amount.value) {
    toast.error("Both fields are required");
    return;
  }

  const transactionData = {
    text: text.value,
    amount: parseFloat(amount.value),
  };

  emit("transactionSubmitted", transactionData);

  text.value = "";
  amount.value = "";
};

// Validate text (only alphabetic characters and spaces are allowed)
const validateText = () => {
  text.value = text.value.replace(/[^a-zA-Z ]/g, "");
};

// Validate amount (only numbers and decimal point are allowed)
const validateAmount = () => {
  amount.value = amount.value.replace(/[^0-9.-]/g, "");
};

// Computed property to check if both text and amount are empty
const isButtonDisabled = computed(() => {
  return !text.value && !amount.value;
});
</script>

<style scoped>
  .disabled-btn:hover {
    cursor: not-allowed;
  }
</style>