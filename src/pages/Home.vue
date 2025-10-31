<template>
  <div class="flex justify-between items-center">
    <h2 class="text-3xl fond-bold mb-8">Все кроссовки</h2>

    <div class="flex gap-4">
      <select @change="onChangeSelect" class="py-2 px-3 border rounded-md outline-none">
        <option value="name">По названию</option>
        <option value="price">По цене (дешевые)</option>
        <option value="-price">По цене (дорогие)</option>
      </select>
      <div class="relative">
        <img src="/search.svg" class="absolute left-4 top-3" alt="" />
        <input
          @change="onChangeInput"
          class="border rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400"
          type="text"
          placeholder="Поиск"
        />
      </div>
    </div>
  </div>
  <div class="mt-10">
    <SneakersCardList
      :items="items"
      @addToFavorite="addToFavorite"
      @addToCart="onClickAddPlus"
    />
  </div>
</template>
<script setup lang="ts">
import SneakersCardList from '@/components/SneakersCardList.vue'
</script>
