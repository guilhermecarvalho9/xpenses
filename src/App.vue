<script setup>
  import Header from './components/Header.vue';
  import Balance from './components/Balance.vue';
  import IncomeExpenses from './components/IncomeExpenses.vue';
  import TransactionList from './components/TransactionList.vue';
  import AddTransaction from './components/AddTransaction.vue';
  import { ref, computed, onMounted } from 'vue';
  import { useToast } from 'vue-toastification';

  const showModal = ref(false); // Controls modal visibility
  const transactionToDelete = ref(null); // Stores the transaction to be deleted
  const toast = useToast(); // Toast notification instance

  const transactions = ref([  
  ]);

  const income = computed(() => {
    return transactions.value
      .filter(transaction => transaction.amount > 0)
      .reduce((acc, transaction) => acc + transaction.amount, 0);
  });

  const expenses = computed(() => {
    return transactions.value
      .filter(transaction => transaction.amount < 0)
      .reduce((acc, transaction) => acc + transaction.amount, 0);
  });

  const total = computed(() => {
    return transactions.value.reduce((acc, transaction) => acc + transaction.amount, 0);
  });


// Methods
  const confirmDeleteTransaction = (transaction) => {
    transactionToDelete.value = transaction;
    showModal.value = true; // Open the modal
  };

  const deleteTransaction = () => {
    transactions.value = transactions.value.filter((t) => t !== transactionToDelete.value);
    addTransactionToLocalStorage(); // Save to local storage
    toast.success('Transaction deleted successfully!'); // Show success message
    transactionToDelete.value = null;
    showModal.value = false; // Close the modal
  };

  const cancelDelete = () => {
    transactionToDelete.value = null;
    showModal.value = false; // Close the modal
  };

  const addTransaction = (transactionData) =>{
    console.log(transactionData);
    const newTransaction = {
      id: transactions.value.length + 1,
      text: transactionData.text,
      category: transactionData.category,
      amount: transactionData.amount,
    };
    transactions.value.push(newTransaction);
    addTransactionToLocalStorage(); // Save to local storage
   
    // Reset the form fields in the AddTransaction component
    text.value = '';
    amount.value = '';
    category.value = ''; 
  }

  const addTransactionToLocalStorage = () => {
    localStorage.setItem('transactions', JSON.stringify(transactions.value));
  }

  // Lifecycle Hooks

  onMounted(() => {
    // Fetch initial transactions from local storage or API
    const storedTransactions = JSON.parse(localStorage.getItem('transactions')) || [];

    if(storedTransactions) {
      transactions.value = storedTransactions;
    }
  });
</script>

<template>
  <Header />
  <div class="container">
    <Balance :total="total" />
    <IncomeExpenses :income="income" :expenses="expenses" />
    <TransactionList
      :transactions="transactions"
      @transactionDeleted="confirmDeleteTransaction"
    />
    <AddTransaction @transactionSubmitted="addTransaction" />

    <!-- Modal -->
    <div v-if="showModal" class="modal">
      <div class="modal-content">
        <p>Are you sure you want to delete this transaction?</p>
        <p>{{ transactionToDelete.text }}</p>
        <p>Amount: ${{ transactionToDelete ? transactionToDelete.amount : '' }}</p>
        <button @click="deleteTransaction">Yes</button>
        <button @click="cancelDelete">No</button>
      </div>
    </div>
  </div>
</template>
