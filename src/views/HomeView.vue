<script setup>
import TheHeader from '@/components/TheHeader.vue'
import { RouterView } from 'vue-router'

import { ref } from 'vue'
const isLogin = ref(false)
</script>

<template>
  <TheHeader v-show="isLogin" />
  <RouterView />
</template>
