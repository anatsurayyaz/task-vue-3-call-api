<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';

const headers = [
  { title: 'Account Name', key: 'name', sortable: true },
  { title: 'Balance', key: 'balance', sortable: true },
  { title: 'Actions', key: 'actions', sortable: false }
];

const accounts = ref([]);
const totalBalance = ref(0);
const averageBalance = ref(0);
const totalAccounts = ref(0);

async function fetchAccounts() {
  try {
    const response = await axios.get('http://localhost:8080/account/list');
    if (response.data && response.data.data) {
      accounts.value = response.data.data; // Update the account list
      totalAccounts.value = accounts.value.length;

      // Calculate total and average balance
      totalBalance.value = accounts.value.reduce((sum, account) => sum + account.balance, 0);
      averageBalance.value = totalAccounts.value > 0 ? totalBalance.value / totalAccounts.value : 0;
    } else {
      console.error('Invalid response structure:', response.data);
    }
  } catch (error) {
    console.error('Error fetching accounts:', error);
  }
}

onMounted(() => {
  fetchAccounts();
});
</script>

<template>
  <v-container>
    <v-row>
      <v-col cols="12">
        <div class="text-center">
          <v-btn @click="fetchAccounts" color="primary" class="mb-4">Load Accounts</v-btn>
        </div>
      </v-col>

      <!-- Account Summary Card -->
      <v-col cols="12" md="6" class="mx-auto">
        <v-card>
          <v-card-title class="text-center" style="font-size: 20px; font-weight: bold;">
            Account Summary
          </v-card-title>
          <v-card-text>
            <v-row>
              <v-col cols="12" sm="4" class="text-center">
                <div><strong>Total Accounts:</strong> {{ totalAccounts }}</div>
              </v-col>
              <v-col cols="12" sm="4" class="text-center">
                <div><strong>Total Balance:</strong> ${{ totalBalance.toFixed(2) }}</div>
              </v-col>
              <v-col cols="12" sm="4" class="text-center">
                <div><strong>Average Balance:</strong> ${{ averageBalance.toFixed(2) }}</div>
              </v-col>
            </v-row>
          </v-card-text>
        </v-card>
      </v-col>

      <!-- Account List Table -->
      <v-col cols="12">
        <v-data-table :headers="headers" :items="accounts" :items-per-page="5">
          <template v-slot:[`item.actions`]="{ item }">
            <v-btn color="red" @click="accounts.splice(accounts.indexOf(item), 1)">
              Delete
            </v-btn>
          </template>
        </v-data-table>
      </v-col>
    </v-row>
  </v-container>
</template>

<style scoped>
.text-center {
  text-align: center;
  margin: 20px 0;
}
</style>
