<script setup>
import { useRouter } from 'vue-router';
import { onMounted } from 'vue';

const router = useRouter()

onMounted(() => {
  setTimeout(() => {
    router.push('/')
  }, 3000)
})

</script>

<template>
  <div class="bg-yellow">
    <div class="container">
      <div class="text-center">
        <h2 class="fs-1 mb-3" >404 Not Found</h2>
        <img class="w-50"
          src="https://raw.githubusercontent.com/hexschool/2022-web-layout-training/main/todolist/img.png"
          alt="workImg"
        />
      </div>
    </div>
  </div>
</template>
