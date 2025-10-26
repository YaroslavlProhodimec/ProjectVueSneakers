<script setup>
import AppHeader from '@/components/AppHeader.vue'
import SneakersCardList from '@/components/SneakersCardList.vue'
import { onMounted, provide, reactive, ref, watch } from 'vue'
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

onMounted(async () => {
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

provide('cart', {
  cart,
  removeFromCart,
  closeDrawer,
  openDrawer,
})
</script>

<template>
  <div class="w-4/5 m-auto h-screen rounded-xl shadow-xl mt-14">
    <AppHeader @open-drawer="openDrawer" />
    <DrawerSide v-if="drawerOpen" @closeDrawer="closeDrawer" />
    <div class="p-10">
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
    </div>
  </div>
</template>

<style scoped></style>
