<script setup>
defineProps(['isLoading'])
</script>

<template>
  <div class="loading loading-position" :class="isLoading ? 'd-flex' : 'd-none'">
    <div className="spinner-border loading-size" role="status">
      <span className="visually-hidden">Loading...</span>
    </div>
  </div>
</template>

<style scoped>
.loading-position{
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  z-index: 1000;
  background-color: rgba(255,255,255,0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
