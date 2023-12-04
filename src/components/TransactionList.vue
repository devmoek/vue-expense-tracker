<template>
  <h3>History</h3>
  <div class="selectors">
    <div class="selector">
      <label for="filter">Filter by:</label>
      <select id="filter" v-model="filterBy" class="dropdown">
        <option value="all">All</option>
        <option value="income">Income</option>
        <option value="expense">Expense</option>
      </select>
    </div>
    <div class="selector">
      <label for="sort">Sort by:</label>
      <select id="sort" v-model="sortBy" class="dropdown">
        <option value="date">Date</option>
        <option value="amount-low-high">Amount (low to high)</option>
        <option value="amount-high-low">Amount (high to low)</option>
        <option value="name-a-z">Name (A to Z)</option>
        <option value="name-z-a">Name (Z to A)</option>
      </select>
    </div>
  </div>
  <ul id="list" class="list">
    <li
      v-for="transaction in filteredTransactions"
      :key="transaction.id"
      :class="transaction.amount < 0 ? 'minus' : 'plus'"
    >
      <span>{{ formattedDate(transaction.date) }}</span>
      <span>{{ transaction.text }}</span>
      <small class="transaction-amount">${{ transaction.amount }}</small>
      <button @click="deleteTransaction(transaction.id)" class="delete-btn">x</button>
    </li>
    <li v-if="filteredTransactions.length === 0" class="no-transactions">
      No transactions yet
    </li>
  </ul>
</template>

<script setup>
import { ref, computed } from 'vue';

const formattedDate = (isoDate) => {
  const date = new Date(isoDate);
  return date.toLocaleDateString(); // Customize the format as needed
};

const sortBy = ref("date");
const filterBy = ref('all'); // Default filter value for showing all transactions

const sortedTransactions = computed(() => {
  let sorted = [];

  if (Array.isArray(props.transactions)) {
    sorted = [...props.transactions]; // Make a copy to avoid mutating original data

    if (sortBy.value === 'date') {
      sorted.sort((a, b) => new Date(b.date) - new Date(a.date));
    } else if (sortBy.value === 'amount-low-high') {
      sorted.sort((a, b) => parseFloat(a.amount) - parseFloat(b.amount));
    } else if (sortBy.value === 'amount-high-low') {
      sorted.sort((a, b) => parseFloat(b.amount) - parseFloat(a.amount));
    } else if (sortBy.value === 'name-a-z') {
      sorted.sort((a, b) => a.text.localeCompare(b.text));
    } else if (sortBy.value === 'name-z-a') {
      sorted.sort((a, b) => b.text.localeCompare(a.text));
    }
  }

  return sorted;
});

const filteredTransactions = computed(() => {
  if (filterBy.value === 'all') {
    return sortedTransactions.value; // Show all transactions without filtering
  } else if (filterBy.value === 'income') {
    return sortedTransactions.value.filter(transaction => transaction.amount > 0);
  } else if (filterBy.value === 'expense') {
    return sortedTransactions.value.filter(transaction => transaction.amount < 0);
  }
});

const emit = defineEmits(["transactionDeleted"]);
const props = defineProps({
  transactions: {
    type: Array,
    required: true,
  },
});

const deleteTransaction = (id) => {
  emit("transactionDeleted", id);
};
</script>

<style>
.no-transactions {
  text-align: center;
  padding: 1rem;
  color: #555;
}

.transaction-amount {
  color: #6c6c6c;
  font-family: monospace;
  font-style: italic;
}

.selectors {
  display: flex;
  justify-content: space-between;
}

span {
  color: #000;
}

.selector {
  margin-bottom: 1rem;
  display: flex;
  align-items: center;
  gap: 10px;
  justify-content: right;
}

.dropdown {
  padding: 0.2rem;
  border-radius: 4px;
  cursor: pointer;
  /* Add any additional styling you prefer */
}
</style>
