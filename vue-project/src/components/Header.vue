<script setup>

const emit = defineEmits(['openDrawer'])


</script>

<template>
  <header class="flex justify-between border-b border-slate-200 px-10 py-8">
    <div class="flex items-center gap-4">
      <img src="/logo.png" alt="Logo" />
      <div>
        <h2 class="text-xl font-bold uppercase">Vue sneakers</h2>
        <p class="text-slate-500">Магазин лучших кроссовок</p>
      </div>
    </div>

    <ul class="flex items-center gap-10">
      <li @click="emit('openDrawer')" class="flex items-center gap-3 cursor-pointer text-gray-500 hover:text-black">
        <img src="/cart.svg" alt="Cart" />
        <b>1205р</b>
      </li>

      <li class="flex items-center gap-3 cursor-pointer text-gray-500 hover:text-black">
        <img src="/heart.svg" alt="Heart" />
        <span>Закладки</span>
      </li>

      <li class="flex items-center gap-3 cursor-pointer text-gray-500 hover:text-black">
        <img src="/profile.svg" alt="Profile" />
        <span>Профиль</span>
      </li>
    </ul>
  </header>
</template>
