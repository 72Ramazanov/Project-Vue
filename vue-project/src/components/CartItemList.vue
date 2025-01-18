<script setup>
import { inject } from 'vue'
import CartItem from './CartItem.vue'

const { cart, removeFromCart } = inject('cart')


</script>

<template>
  <div class="flex flex-col gap-4">
    <CartItem
      v-for="item in cart"
      :key="item.id"
      :id="item.id"
      :imageUrl="item.imageUrl"
      :title="item.title"
      :price="item.price"
      @on-click-remove="() => removeFromCart(item)"
    />
  </div>
</template>
