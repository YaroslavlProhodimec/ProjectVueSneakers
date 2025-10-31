<script setup>
import SneakerCard from '/src/components/SneakerCard.vue'

defineProps({
  items: Array,
})
const emit = defineEmits(['addToFavorite', 'addToCart'])
</script>
<template>
  <div class="grid grid-cols-4 gap-5" v-auto-animate>
    <SneakerCard
      v-for="item in items"
      :key="item.id"
      :image-url="item.imageUrl"
      :title="item.title"
      :price="item.price"
      :is-added="item.isAdded"
      :is-favourite="true"
      :on-click-add="()=>emit('addToCart',item)"
      :on-click-favorite="() => emit('addToFavorite', item)"
      :is-favorite="item.isFavorite"
    />
  </div>
</template>
