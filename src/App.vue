<script setup>
import AppHeader from '@/components/AppHeader.vue'
import SneakersCardList from '@/components/SneakersCardList.vue'
</script>

<template>
  <div class="w-4/5 m-auto h-screen rounded-xl shadow-xl mt-14">
    <AppHeader />
    <div class="p-10">
    <h2 class="text-3xl fond-bold mb-8">Все кроссовки</h2>
    <SneakersCardList/>
    </div>
  </div>
</template>

<style scoped></style>
