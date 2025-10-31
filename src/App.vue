<script setup>
import AppHeader from '@/components/AppHeader.vue'
import SneakersCardList from '@/components/SneakersCardList.vue'
import { computed, onMounted, provide, reactive, ref, watch } from 'vue'
import axios from 'axios'
import DrawerSide from '@/components/DrawerSide.vue'
// import DrawerSide from '@/components/DrawerSide.vue'
const items = ref([])
const cart = ref([])

const filters = reactive({
  sortBy: 'title',
  searchQuery: '',
})
const addToCart = (item) => {
  cart.value.push(item)
  item.isAdded = true
}

const removeFromCart = (item) => {
  cart.value.splice(cart.value.indexOf(item), 1)
  item.isAdded = false
}
const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item)
  } else {
    removeFromCart(item)
  }
}

const onChangeSelect = (event) => {
  filters.sortBy = event.target.value
}
const onChangeInput = (event) => {
  filters.searchQuery = event.target.value
}

const addToFavorite = async (item) => {
  // item.isFavorite = !item.isFavorite
  try {
    if (!item.isFavorite) {
      const obj = {
        parentsId: item.id,
      }
      const { data } = await axios.post('https://5ac01713d7a29def.mokky.dev/favorites', obj)

      item.isFavorite = true
      item.favoriteId = data.id
    } else {
      await axios.delete(`https://5ac01713d7a29def.mokky.dev/favorites/${item.favoriteId}`)
      item.isFavorite = false
      item.favoriteId = null
    }
  } catch (e) {
    console.log(e)
  }
}

const fetchItems = async () => {
  const params = {
    sortBy: filters.sortBy,
  }
  if (filters.searchQuery) {
    params.title = `*${filters.searchQuery}`
  }

  try {
    const { data } = await axios.get('https://5ac01713d7a29def.mokky.dev/items', { params })

    items.value = data.map((item) => ({
      ...item,
      isFavorite: false,
      isAdded: false,
    }))
  } catch (e) {
    console.log(e)
  }
}
const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get('https://5ac01713d7a29def.mokky.dev/favorites')

    items.value = items.value.map((item) => {
      const favorite = favorites.find((favorite) => favorite.parentId === item.id)

      if (!favorite) {
        return item
      }

      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id,
      }
    })
  } catch (e) {
    console.log(e)
  }
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
onMounted(async () => {
  const localCart = localStorage.getItem('cart', JSON.stringify(cart.value))
  cart.value = localCart ? JSON.parse(localCart) : []

  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((el) => el.id === item.id )
  }))

  await fetchItems()
  await fetchFavorites()
})
watch(filters, fetchItems)
const drawerOpen = ref(false)
const closeDrawer = () => {
  drawerOpen.value = false
}
const openDrawer = () => {
  drawerOpen.value = true
}
watch(cart, () => {
  console.log(cart, 'cart')
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false,
  }))
})

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

    </div>
  </div>
</template>

<style scoped></style>
