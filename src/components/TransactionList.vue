<template>
    <h3>History</h3>
    <ul id="list" class="list">
        <li v-for="transaction in transactions" :key="transaction.id"
            :class="transaction.amount < 0 ? 'minus' : 'plus'">
            {{ transaction.text }} <span>${{ transaction.amount }}</span><button
                @click="deleteTransaction(transaction.id)" class="delete-btn">x</button>

        </li>

    </ul>
</template>

<script setup lang="ts">
// import { defineProps } from 'vue';
import type { Transaction } from './types';

const emit = defineEmits(['transactionDeleted']);

const props = defineProps({
    transactions: {
        type: Array as () => Transaction[],
        required: true,
    },
})

const deleteTransaction = (id: Transaction['id']) => {
    emit('transactionDeleted', id)
}

</script>