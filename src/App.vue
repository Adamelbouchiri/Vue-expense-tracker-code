<template>
  <HeaderCom />

  <div class="container">
    <Balance :total="+total" />
    <IncomeExpensesCom :income="+income" :expenses="+expenses" />
    <TransactionList
      :transactions="transactions"
      @transactionDeleted="handleTransactionDeleted"
    />
    <AddTransactionCom @transactionSubmitted="handleTransactionSubmitted" />
  </div>
</template>

<script setup>
import HeaderCom from "./components/HeaderCom.vue";
import Balance from "./components/Balance.vue";
import IncomeExpensesCom from "./components/IncomeExpensesCom.vue";
import TransactionList from "./components/TransactionListCom.vue";
import AddTransactionCom from "./components/AddTransactionCom.vue";

import { ref, computed, onMounted } from "vue";
import { useToast } from "vue-toastification";

const toast = useToast();

const transactions = ref([]);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem("transactions"));

  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
});

// Get Total
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0);
});

// Get Income
const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0)
    .toFixed(2);
});

// Get Expenses
const expenses = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0)
    .toFixed(2);
});

// Add Transaction
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount,
  });

  savedTransactionToLocalStorage();

  toast.success("Transaction Added Successfully");
};

// Generate Unique ID
const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000000);
};

// Delete Transaction
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter(
    (transaction) => transaction.id !== id
  );

  savedTransactionToLocalStorage();

  toast.success("Transaction Deleted");
};

// Save To LocalStorage
const savedTransactionToLocalStorage = () => {
  localStorage.setItem("transactions", JSON.stringify(transactions.value));
};
</script>

<style lang="css" scoped></style>
