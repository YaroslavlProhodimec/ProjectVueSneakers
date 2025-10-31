<script setup>
import CartItem from '/src/components/CartItem.vue'
import { inject } from 'vue'

const { cart, removeFromCart } = inject('cart', 'removeFromCart')
</script>
<template>
  <div class="flex flex-col gap-3" v-auto-animate>
    <CartItem
      v-for="item in cart"
      :key="item.id"
      id="item.id"
      :price="item.price"
      :image-url="item.imageUrl"
      :title="item.title"
      @on-click-remove="() => removeFromCart(item)"
    />
  </div>
</template>
