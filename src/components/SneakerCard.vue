<script setup lang="ts">
defineProps({
  imageUrl: String,
  title: String,
  price: Number,
  isFavourite: Boolean,
  isAdded: Boolean,
  onClickAdd: Function,
  onClickAddFavourite: Function,
})
</script>

<template>
  <div
    class="relative bg-white border border-slate-100 rounded-3xl p-8 cursor-pointer transition hover:-translate-y-2 hover:shadow-xl"
  >
    <img
      :src="isFavourite ? '/like-2.svg' : '/like-1.svg'"
      alt="like"
      class="absolute top-0 left-0"
      @click="onClickAddFavourite"
    />
    <img :src="imageUrl" alt="Sneakers" />
    <p class="mt-2">{{ title }}</p>
    <div class="flex justify-between mt-5">
      <div class="flex flex-col">
        <b>Цена </b>
        <span class="text-slate-400">{{ price }} руб.</span>
      </div>
      <img :src="isAdded ? '/checked.svg' : '/plus.svg'"
           alt="Plus"
           @click="onClickAddFavourite" />
    </div>
  </div>
</template>
