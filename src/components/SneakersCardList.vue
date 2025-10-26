<script setup>
import SneakerCard from '/src/components/SneakerCard.vue'

defineProps({
  items: Array,
})
const emit = defineEmits(['addToFavorite'])
const handleClickAdd = () => {}
</script>
<template>
  <div class="grid grid-cols-4 gap-5">
    <SneakerCard
      v-for="item in items"
      :key="item.id"
      :image-url="item.imageUrl"
      :title="item.title"
      :price="item.price"
      :is-added="false"
      :is-favourite="true"
      :on-click-add="handleClickAdd"
      :on-click-favorite="() => emit('addToFavorite', item)"
      :is-favorite="item.isFavorite"
    />
  </div>
</template>
