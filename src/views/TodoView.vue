<script setup>
import axios from 'axios'

import TheHeader from '@/components/TheHeader.vue'
import MessageToast from '@/components/MessageToast.vue'
import empty from '@/assets/images/empty 1.png'

import { ref, onMounted, computed, provide } from 'vue'
import { useRouter } from 'vue-router'

const api = import.meta.env.VITE_BASE_URL
const router = useRouter()

// message
const messageText = ref([])
const isShow = ref(false)

provide('message', {
  messageText,
  isShow,
})

const createMessage = (text, status) => {
  const id = new Date().getTime()
  messageText.value.push({
    id,
    text,
    status,
  })
  isShow.value = true
  setTimeout(() => {
    const index = messageText.value.findIndex((i) => i.id === id)
    if (index !== -1) {
      messageText.value.splice(index, 1)
    }
    isShow.value = false
  }, 3000)
}

// 驗證 + 取得代辦事項
const token = document.cookie.replace(/(?:^|.*;\s*)TodoToken\s*=\s*([^;]*).*$/i, '$1')
const nickname = ref('')
onMounted(async () => {
  try {
    const res = await axios.get(`${api}users/checkout`, {
      headers: {
        Authorization: token,
      },
    })
    nickname.value = res.data.nickname
    getTodo()
    createMessage('登入成功', true)
  } catch (error) {
    console.log('錯誤', error)
    router.push('/')
  }
})

// 取得Todo
const todoList = ref([])
const getTodo = async (status) => {
  try {
    const res = await axios.get(`${api}todos/`, {
      headers: {
        Authorization: token,
      },
    })
    todoList.value = res.data.data
    if (status === 'finish') {
      todoList.value = todoList.value.filter((todo) => todo.status !== false)
    } else if (status === 'finishYet') {
      todoList.value = todoList.value.filter((todo) => todo.status !== true)
    }
  } catch (error) {
    console.log(error)
  }
}

// 待完成個數
const todoNum = computed(() => {
  return todoList.value.filter((todo) => todo.status !== true).length
})

// 新增Todo
const todoContent = ref({
  content: '',
})
const addTodo = async (todoContent) => {
  try {
    await axios.post(`${api}todos/`, todoContent, {
      headers: {
        Authorization: token,
      },
    })
    getTodo()
    todoContent.content = ''
    createMessage('新增成功', true)
  } catch (error) {
    console.log(error)
    createMessage('新增失敗', false)
  }
}

// 刪除 Todo
const deleteTodo = async (id) => {
  try {
    await axios.delete(`${api}todos/${id}`, {
      headers: {
        Authorization: token,
      },
    })
    getTodo()
    createMessage('刪除成功', true)
  } catch (error) {
    console.log(error)
    createMessage('刪除失敗', false)
  }
}

// todo status
const toggleTodo = async (todo) => {
  try {
    const res = await axios.patch(`${api}todos/${todo.id}/toggle`, null, {
      headers: {
        Authorization: token,
      },
    })
    todo.status = res.data.status
    createMessage('代辦事項完成', true)
  } catch (error) {
    console.log(error)
  }
}
</script>

<template>
  <MessageToast />
  <div id="todoListPage" class="bg-half">
    <TheHeader :token="token" :nickname="nickname" />
    <div class="container todoListPage vhContainer">
      <div class="todoList_Content">
        <div class="inputBox">
          <input type="text" placeholder="請輸入待辦事項" v-model="todoContent.content" />
          <a href="#" @click.prevent="addTodo(todoContent)">
            <i class="bi bi-plus-lg"></i>
          </a>
        </div>
        <div v-if="todoList.length < 1">
          <p class="text-center fs-5 mt-5 mb-4">目前無代辦事項</p>
          <img :src="empty" alt="" class="w-auto d-block mx-auto object-fit-cover" />
        </div>
        <div class="todoList_list" v-else>
          <ul class="todoList_tab">
            <li><a href="#" class="active" @click.prevent="getTodo('all')">全部</a></li>
            <li><a href="#" class="active" @click.prevent="getTodo('finishYet')">待完成</a></li>
            <li><a href="#" class="active" @click.prevent="getTodo('finish')">已完成</a></li>
          </ul>
          <div class="todoList_items">
            <ul class="todoList_item">
              <li v-for="todo in todoList" :key="todo.id">
                <label class="todoList_label">
                  <input
                    class="todoList_input"
                    type="checkbox"
                    @click="toggleTodo(todo)"
                    :disabled="todo.status === true"
                    :checked="todo.status === true"
                  />
                  <span>{{ todo.content }}</span>
                </label>
                <button type="button" class="btn" @click.prevent="deleteTodo(todo.id)">
                  <i class="bi bi-x-lg"></i>
                </button>
              </li>
            </ul>
            <div class="todoList_statistics">
              <p>{{ todoNum }} 個待完成項目</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
