<script setup>
import SneakersCardList from '../components/SneakersCardList.vue'
import axios from 'axios'
import { inject, onMounted, reactive, ref, watch } from 'vue'

const {cart,addToCart} = inject('cart')
const items = ref([])

watch(cart, () => {
  console.log(cart, 'cart')
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false,
  }))
})
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


const filters = reactive({
  sortBy: 'title',
  searchQuery: '',
})

// const onClickAddPlus = (item) => {
//   if (!item.isAdded) {
//     addToCart(item)
//   } else {
//     removeFromCart(item)
//   }
// }

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
        item
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
watch(filters, fetchItems)
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
</script>

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
      <SneakersCardList :items="items" @addToFavorite="addToFavorite" @addToCart="addToCart" />
    </div>
</template>
