<script setup>
import SneakerCard from '/src/components/SneakerCard.vue'
const handleClickAdd = () => {

}

</script>
<template>
  <div class="grid grid-cols-4 gap-5">
    <SneakerCard
      image-url="sneakers/sneakers-1.jpg"
      title="Мужские Кроссовки Nike Blazer Mid Suede"
      :price="5000"
      :is-added="false"
      :is-favourite="true"
       :on-click-add="handleClickAdd"
       :on-click-add-favourite="onClickAddFavourite"
    />
  </div>
</template>
