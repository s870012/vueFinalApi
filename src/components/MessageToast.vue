<script setup>
import { inject } from 'vue'

const { messageText, isShow } = inject('message', 'isShow')

const closeToast = (id) => {
  const index = messageText.value.findIndex((i) => i.id === id)
  if (index !== -1) {
    messageText.value.splice(index, 1)
  }
}
</script>

<template>
  <div class="position-fixed top-0 end-0 p-3" style="z-index: 1050">
    <div
      class="toast show align-items-center text-white border-0 mb-2"
      :class="message.status ? 'bg-success' : 'bg-danger'"
      v-for="message in messageText"
      :key="message.id"
    >
      <div class="d-flex" v-show="isShow">
        <div class="toast-body">{{ message.text }}</div>
        <button
          type="button"
          class="btn-close btn-close-white me-2 m-auto"
          @click="closeToast(message.id)"
        ></button>
      </div>
    </div>
  </div>
</template>
