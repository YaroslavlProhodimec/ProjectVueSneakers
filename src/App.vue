<script setup>
import AppHeader from '@/components/AppHeader.vue'
import { computed, provide, ref, watch } from 'vue'
import axios from 'axios'
import DrawerSide from '@/components/DrawerSide.vue'
// import DrawerSide from '@/components/DrawerSide.vue'
import Home from './pages/Home.vue'
const cart = ref([])

const addToCart = (item) => {
  cart.value.push(item)
  item.isAdded = true
}


const removeFromCart = (item) => {
  cart.value.splice(cart.value.indexOf(item), 1)
  item.isAdded = false
}
const isCreatingOrder = ref(false)
const createOrder = async () => {
  try {
    isCreatingOrder.value = true
    const { data } = await axios.post('https://5ac01713d7a29def.mokky.dev/orders', {
      items: cart.value,
      totalPrice: cart.value,
    })

    cart.value = []

    return data
  } catch (e) {
    isCreatingOrder.value = false

    console.log(e)
  } finally {
    isCreatingOrder.value = false
  }
}

const drawerOpen = ref(false)
const closeDrawer = () => {
  drawerOpen.value = false
}
const openDrawer = () => {
  drawerOpen.value = true
}

watch(
  cart,
  () => {
    localStorage.setItem('cart', JSON.stringify(cart.value))
  },
  { deep: true },
)

provide('cart', {
  cart,
  removeFromCart,
  closeDrawer,
  openDrawer,
  addToCart
})

const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0))
const vatPrice = computed(() => Math.round((totalPrice.value * 5) / 100))
const cartIsEmpty = computed(() => cart.value.length === 0)
const cartButtonDisabled = computed(() => isCreatingOrder.value || cartIsEmpty.value)
</script>

<template>
  <div class="w-4/5 m-auto h-screen rounded-xl shadow-xl mt-14">
    <AppHeader :total-price="totalPrice" @open-drawer="openDrawer" />
    <DrawerSide
      :disableButton="cartButtonDisabled"
      @create-order="createOrder"
      v-if="drawerOpen"
      :total-price="totalPrice"
      :vat-price="vatPrice"
      @closeDrawer="closeDrawer"
    />
    <div class="p-10">
      <Home/>
    </div>
  </div>
</template>

<style scoped></style>
