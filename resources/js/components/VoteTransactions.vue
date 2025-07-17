<script setup>
import { ref } from 'vue'
import moment from 'moment-timezone'

defineProps(['transactions', 'name'])

// State to player info display state for admin
const showPayerInfo = ref(false)
</script>


<template>
    <h1 class="text-base xsm:text-lg font-semibold text-neutral-700">{{ name  }} Votes Timeline</h1>

    <div v-if="transactions && transactions.length" class="flex w-full flex-col items-start ">
        <div v-for="transaction in transactions" :key="transaction.created_at" class="timel group flex gap-x-6">
            <div class="relative">
                <div class="absolute left-1/2 top-0 h-full w-0 border border-dashed -translate-x-1/2 border-neutral-500"></div>
                <span class="relative z-10 grid h-3 w-3 place-items-center rounded-full bg-neutral-500 text-slate-800"></span>
            </div>
            <div class="content -translate-y-1.5 pb-8">
                <p class="font-sans text-sm text-neutral-600 antialiased">{{ moment(transaction.created_at).format('dddd, DD MMM YYYY, hh:mma') }}</p>
                <small class="mt-2 font-sans text-sm font-bold text-blue-600 antialiased mb-1">
                    Received {{ transaction.votes }}. Brought total votes to {{ transaction.votes + transaction.prev_votes }}, from previous {{ transaction.prev_votes }}.
                </small>

                <template v-if="$page.props.auth.user">
                    <button @click="showPayerInfo = transaction.id" class="text-sm underline underline-offset-4 text-neutral-700">Payer Info (Admin only)</button>

                    <div v-if="$page.props.auth.user && showPayerInfo === transaction.id" class="w-fit text-sm border border-neutral-300 text-neutral-700 p-3 rounded-md mt-2">
                        <strong>Email: </strong> <span class="mr-4">{{ transaction.email }}</span><br>
                        <strong>Phone: </strong> <span class="mr-4">{{ transaction.phone }}</span><br>
                        <strong>Amount Payed: </strong> <span>{{ (transaction.amount/100).toLocaleString() }}</span>
                    </div>
                </template>
            </div>
        </div>
    </div>


    <div v-else class="text-2xl text-neutral-500">
        No vote transactions yet.
    </div>

</template>


<style>
.timel:last-child .content {
    padding-bottom: 0;
}
</style>