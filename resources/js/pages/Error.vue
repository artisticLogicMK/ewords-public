<script setup>
import { Link, Head, usePage } from '@inertiajs/vue3'
import AppLayout from '@/layouts/AppLayout.vue'
import PagesHeader from '@/components/PagesHeader.vue'

const { status } = defineProps(['status'])

const page = usePage()

const breadcumb = [
    { name: "Home", url: "/" },
    { name: `${status} Error`, url: page.url }
]
</script>


<template>

    <Head :title="`${status} Error`" />

    <AppLayout>

        <PagesHeader
            :title="`${status} Error`"
            :breadcumb="breadcumb"
            class="mb-20 scale-in"
        />

        <main class="w-full max-w-3xl mx-auto mb-30 px-4 sm:px-6">


            <h1 class="text-5xl font-bold text-red-500 mb-4">Error {{ status }}</h1>
            <p class="text-lg text-gray-700">
                <span v-if="status === 404">Sorry, the page you are looking for could not be found.</span>
                <span v-else-if="status === 500">Oops! Something went wrong on our end.</span>
                <span v-else>Something went wrong.</span>
            </p>
            <a href="/" class="mt-6 text-blue-600 hover:underline">Go back home</a>


        </main>

    </AppLayout>
</template>