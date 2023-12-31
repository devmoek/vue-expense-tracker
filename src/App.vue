<template>
  <Header />
  <div class="container">
    <Balance :total="+total" />
    <IncomeExpenses :income="+income" :expenses="+expenses" />
    <TransactionList
      :transactions="transactions"
      @transactionDeleted="handleTransactionDeleted"
    />
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
  </div>
</template>

<script setup>
import Header from "./components/Header.vue";
import Balance from "./components/Balance.vue";
import IncomeExpenses from "./components/IncomeExpenses.vue";
import TransactionList from "./components/TransactionList.vue";
import AddTransaction from "./components/AddTransaction.vue";

import { useToast } from "vue-toastification";

import { ref, computed, onMounted } from "vue";

const toast = useToast();

const transactions = ref([]);

onMounted(() => {
  const savedTrasactions = JSON.parse(localStorage.getItem("transactions"));

  if (savedTrasactions) {
    transactions.value = savedTrasactions;
  }
});

// Get total
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => acc + transaction.amount, 0);
});

// Get income
const income = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return transaction.amount > 0 ? acc + transaction.amount : acc;
  }, 0).toFixed(2);
});

// Get expenses
const expenses = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return transaction.amount < 0 ? acc + transaction.amount : acc;
  }, 0).toFixed(2);
});

// Add transaction
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount,
    date: new Date(),
  });

  saveTransactionsToLocalStorage();

  toast.success("Transaction added successfully");
};

// Generate unique ID
const generateUniqueId = () => {
  const ID = crypto.randomUUID();
  return ID;
};

// Delete transaction
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter(
    (transaction) => transaction.id !== id
  );

  saveTransactionsToLocalStorage();

  toast.success("Transaction deleted successfully");
};

// Save to local storage
const saveTransactionsToLocalStorage = () => {
  localStorage.setItem("transactions", JSON.stringify(transactions.value));
};
</script>
