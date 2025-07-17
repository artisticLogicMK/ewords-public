<script setup>
import { Link, Head } from '@inertiajs/vue3'
import AppLayout from '@/layouts/AppLayout.vue'
import PagesHeader from '@/components/PagesHeader.vue'

const { terms } = defineProps(['terms'])

const breadcumb = [
    { name: "Home", url: "/" },
    { name: "Terms and Conditions", url: "/terms" }
]
</script>


<template>

    <Head title="Terms and Conditions" />

    <AppLayout>

        <PagesHeader
            title="Terms and Conditions"
            :breadcumb="breadcumb"
            class="mb-20 scale-in"
        />

        <main class="w-full max-w-3xl mx-auto mb-30 px-4 sm:px-6">


          <h1 class="text-2xl sm:text-3xl text-[var(--echo-dark-400)] barlow-condensed-bold mb-5 text-center">
            Terms and Conditions
          </h1>


          <div class="w-full torch-doc pb-5 mb-5" v-html="terms.content"></div>


        </main>

    </AppLayout>
</template>