<script setup>
import AppHeader from '@/components/AppHeader.vue'
import SneakerCard from '@/components/SneakerCard.vue'
</script>

<template>
  <div class="w-4/5 m-auto h-screen rounded-xl shadow-xl mt-14">
    <AppHeader />
    <h2 class="text-3xl fond-bold">Все кроссовки</h2>
    <div>
    <SneakerCard/>
    </div>
  </div>
</template>

<style scoped></style>
