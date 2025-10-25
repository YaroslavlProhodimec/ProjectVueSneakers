<script setup>
import AppHeader from '@/components/AppHeader.vue'
import SneakersCardList from '@/components/SneakersCardList.vue'
// import DrawerSide from '@/components/DrawerSide.vue'
</script>

<template>
  <div class="w-4/5 m-auto h-screen rounded-xl shadow-xl mt-14">
    <AppHeader />
<!--    <DrawerSide />-->
    <div class="p-10">
      <div class="flex justify-between">
        <h2 class="text-3xl fond-bold mb-8">Все кроссовки</h2>
        <select name="" id="">
          <option value=""></option>
          <option value=""></option>
          <option value=""></option>
        </select>
        <div class="relative">
          <img src="/search.svg" class="absolute left-4 top-3" alt="">
          <input class="border rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400" type="text" placeholder="Поиск">
        </div>
      </div>
    <SneakersCardList/>
    </div>
  </div>
</template>

<style scoped></style>
