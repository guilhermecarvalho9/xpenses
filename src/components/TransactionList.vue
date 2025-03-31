<script setup>
import { defineProps, computed } from 'vue';
import CategoryChart from './CategoryChart.vue';

const props = defineProps({
  transactions: {
    type: Array,
    required: true,
  },
});

const emit = defineEmits(['transactionDeleted']);

const deleteTransaction = (transaction) => {
  emit('transactionDeleted', transaction);
};

// Calculate income and expense categories
const incomeCategories = computed(() => {
  const income = props.transactions.filter((t) => t.amount > 0);
  const categoryTotals = income.reduce((acc, t) => {
    acc[t.category] = (acc[t.category] || 0) + t.amount;
    return acc;
  }, {});
  return categoryTotals;
});

const expenseCategories = computed(() => {
  const expenses = props.transactions.filter((t) => t.amount < 0);
  const categoryTotals = expenses.reduce((acc, t) => {
    acc[t.category] = (acc[t.category] || 0) + Math.abs(t.amount);
    return acc;
  }, {});
  return categoryTotals;
});

// Prepare chart data
const incomeChartData = computed(() => ({
  labels: Object.keys(incomeCategories.value),
  datasets: [
    {
      label: 'Income by Category',
      data: Object.values(incomeCategories.value),
      backgroundColor: ['#2ecc71', '#27ae60', '#1abc9c', '#16a085'],
    },
  ],
}));

const expenseChartData = computed(() => ({
  labels: Object.keys(expenseCategories.value),
  datasets: [
    {
      label: 'Expenses by Category',
      data: Object.values(expenseCategories.value),
      backgroundColor: ['#e74c3c', '#c0392b', '#e67e22', '#d35400'],
    },
  ],
}));
</script>

<template>
    <h3>History</h3>
    <ul id="list" class="list">
      <li
        v-for="transaction in transactions"
        :key="transaction.id"
        :class="transaction.amount < 0 ? 'minus' : 'plus'"
      >
        {{ transaction.text }} <span>{{ transaction.category }}</span
        > <span>${{ transaction.amount }}</span
        ><button class="delete-btn" @click="deleteTransaction(transaction)">
          x
        </button>
      </li>
    </ul>
  
    <!-- Pie Charts -->
    <div class="charts" v-if="transactions.length > 0">
      <div class="chart">
        <h4>Income by Category</h4>
        <CategoryChart :data="incomeChartData" />
      </div>
      <div class="chart">
        <h4>Expenses by Category</h4>
        <CategoryChart :data="expenseChartData" />
      </div>
    </div>
    <div v-else>
      <p>No transaction found</p>
    </div>
  </template>

