<script setup>
  import { useToast } from 'vue-toastification';
  import { ref } from 'vue';
  
  const text = ref('');
  const amount = ref('');
  const category = ref('');
  
  // Get toast interface
  const toast = useToast();
  
  const emit = defineEmits(['transactionSubmitted']);
  
  const onSubmit = () => {
    if (!text.value || !amount.value) {
      // Display a toast error message if either field is empty
      toast.error('Both fields must be filled.');
      return;
    }
  
    const transactionData = {
      text: text.value,
      amount: parseFloat(amount.value),
      category: category.value,
    };

    toast.success('Transaction added successfully!');
  
    emit('transactionSubmitted', transactionData);

    // Reset form fields
    text.value = '';
    amount.value = '';
    category.value = '';
  
    // Clear form fields
    
  };
  </script>


<template>
    <h3>Add new transaction</h3>
    <form id="form" @submit.prevent="onSubmit">
      <div class="form-control">
        <label for="text">Transaction Name</label>
        <input type="text" id="text" placeholder="Enter name..." v-model="text" />
      </div>
      <div class="form-control">
        <label for="amount"
          >Amount <br />
          (negative - expense, positive - income)</label
        >
        <input
          type="text"
          id="amount"
          placeholder="Enter amount..."
          v-model="amount"
        />
        
      </div>
      <div class="form-control">
        <label for="category">Category </label>
          <br />
          
        <input
          type="text"
          id="category"
          placeholder="Enter category..."
          v-model="category"
        />
      </div>
      <button class="btn">Add transaction</button>
    </form>
  </template>
  
  