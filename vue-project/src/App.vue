<!-- eslint-disable no-unused-vars -->
<script setup>
import { onMounted, provide, reactive, ref, watch } from 'vue'

import Header from './components/Header.vue'
import CardList from './components/CardList.vue'
import axios from 'axios'
import Drawer from './components/Drawer.vue';

console.log('333')

const items = ref([]) //Массив,который хранит все объекты из вне
const cart = ref([])

const drawerOpen = ref(false)

const closeDrawer = () => {
  drawerOpen.value = false
}

const openDrawer = () => {
  drawerOpen.value = true
}


const filters = reactive({
  //Объект в котором хранятся наши фильтры
  sortBy: 'title',
  searchQuery: ''
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

//Далее идут две функции, которые следять за изменением селекта и функции
const onChangeSelect = (event) => {
  filters.sortBy = event.target.value
}

const onChangeSearchInput = (event) => {
  filters.searchQuery = event.target.value
}



const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get(`https://0dcd4b2097388a30.mokky.dev/favorites`)
    items.value = items.value.map(item => {
      const favorite = favorites.find(favorite => favorite.parentId === item.id);

      if (!favorite) {
        return item
      } else {
        return{
          ...item, 
          isFavorite: true,
          favoriteId: favorite.id
        }
      }
    }) 
  } catch (err) {
    console.log(err)
  }
}

//Здесь идет функция которая при каждом изменение списка или же рендеринге будет выпольнять запрос на бэк
const fetchItems = async () => {
  try {
    const params = {
      //Здесь мы по умолчанию при рендеринге или фильтрации мы передоем сортировку
      sortBy: filters.sortBy
    }

    if (filters.searchQuery) {
      //При этом проверяем изменился ли параметр в инпуте и в такоем случае мы будем менять параметр на верху(на бэк)
      params.title = `*${filters.searchQuery}*` //Это вишки  MOKKY.dev
    }

    const { data } = await axios.get(`https://0dcd4b2097388a30.mokky.dev/items`, { params }) //Тут мы делаем запрос на бэк и передаем данные в params 
    //Алтернатива ( `https://0dcd4b2097388a30.mokky.dev/items?title=*${filters.searchQuery}*&sortBy=${filters.sortBy}`)

    items.value = data.map((obj) => ({
      ...obj, 
      isFavorite: false,
      favoriteId: null,
      isAdded: false
    }))//добавляем данные в items и сразу же прописываем два состояния
  } catch (err) {
    console.log(err)
  }
}

const  addToFavorite = async (item) => {
  try {
    if(!item.isFavorite) { //Если итем не был добавлен в закладки то мы создаем закладку
      const obj = {
        parentId: item.id
      }
      item.isFavorite = true;

      const { data} = await axios.post(`https://0dcd4b2097388a30.mokky.dev/favorites`, obj)
      
      item.favoriteId = data.id;

    } else {
      item.isFavorite = false; 

      await axios.delete(`https://0dcd4b2097388a30.mokky.dev/favorites/${item.favoriteId}`)
      
      item.favoriteId = null;

    }
  } catch (err) {
    console.log(err)
  }
}

provide('cart', {
  cart,
  addToCart,
  removeFromCart,
  openDrawer, 
  closeDrawer
})
provide('close', closeDrawer)

onMounted( async() => {
  await fetchItems();
  await fetchFavorites();
})
watch(filters, fetchItems)
</script> 

<template>
  <Drawer v-if="drawerOpen"/>

  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
    <Header @open-drawer="openDrawer" />

    <div class="p-10">
      <div class="flex justify-between items-center">
        <h3 class="text-3xl font-bold mb-8">Все кроссовки</h3>

        <div class="flex gap-4">
          <select @change="onChangeSelect" class="py-2 px-3 border rounded-md outline-none">
            <option value="name">По названию</option>
            <option value="price">По цене (дешевые)</option>
            <option value="-price">По цене (дорогие)</option>
          </select>

          <div class="relative">
            <img class="absolute left-4 top-2.5" src="/search.svg" alt="" />
            <input
              @input="onChangeSearchInput"
              class="border rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400"
              type="text"
              placeholder="Поиск...."
            />
          </div>
        </div>
      </div>

      <div class="mt-10">
       <CardList :items="items" @add-to-favorite="addToFavorite" @add-to-cart="onClickAddPlus" />   <!-- сюда мы передаем пропс котрый внутри файла будет перебирать с помощью директивы фор -->
      </div>
    </div>
  </div>
</template>

<style scoped></style>
