<script setup>
defineProps(['name', 'picture', 'position', 'emoji'])
</script>

<template>
    <div class="contcards flex flex-col justify-between w-full max-w-sm mx-auto items-center border bdr rounded-lg mb-5 sm:mb-0 last:mb-0 p-3 py-5 shadow-sm">
      <h1 class="text-base sm:text-lg font-bold text-[var(--echo-dark-400)] text-center mb-1">{{ emoji }} {{ name }}</h1>
      <p class="text-xs text-neutral-600/80 text-center mb-3">
        <strong>{{ name }}</strong> emerged as the <strong>{{ position }}</strong>
      </p>
      
      <div class="shrink-0 w-60 h-60 relative">
        <img
          :src="picture ? `/storage/${picture}` : '/assets/default_contestant.png'"
          class="w-full h-full object-cover object-center rounded-md" />
      </div>
    </div>
</template>

<style scoped>
@reference "@/css/app.css";

.contcards {
  transition: all 0.3s ease;
}

.contcards:hover {
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
}
</style>