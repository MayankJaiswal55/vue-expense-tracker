<script setup lang="ts">
import Header from './components/Header.vue';
import Balance from './components/Balance.vue'
import IncomeExpenses from './components/IncomeExpenses.vue'
import TransactionList from './components/TransactionList.vue'
import AddTransaction from './components/AddTransaction.vue'
import { useToast } from 'vue-toastification'
import { ref, computed, onMounted } from 'vue';
import type { Transaction } from './components/types';

const toast = useToast();
const transactions = ref([
  { id: 1, text: "Flower", amount: -29.99 },
  { id: 2, text: "Salary", amount: 299.99 }
]);

onMounted(() => {
  const savedTransactionsString = localStorage.getItem('transactions');
  const savedTransactions = savedTransactionsString ? JSON.parse(savedTransactionsString) : null;

  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
});

const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0)
});

const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0).toFixed(2);
});

const expenses = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0).toFixed(2);
});
const handleTransactionSubmitted = (transactionData: Transaction) => {
  transactions.value.push({
    id: generateUniqueID(),
    text: transactionData.text,
    amount: transactionData.amount
  });
  saveTransactionsToLocalStorage();
  toast.success('Transaction Added')
}

const generateUniqueID = () => {
  return Math.floor(Math.random() * 1000000)
}
const handleTransactionDeleted = (id: Transaction['id']) => {
  console.log(id);
  transactions.value = transactions.value.filter((transaction) => transaction.id !== id)
  saveTransactionsToLocalStorage();
  toast.success('Transaction Deleted')
}

const saveTransactionsToLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value))
}

</script>

<template>
  <Header></Header>
  <div class="container">
    <Balance :total="+total"></Balance>
    <IncomeExpenses :income="+income" :expenses="+expenses"></IncomeExpenses>
    <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted"></TransactionList>
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted"></AddTransaction>
  </div>
</template>

<style scoped></style>
