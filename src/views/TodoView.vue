<script setup>
import TheHeader from '@/components/TheHeader.vue'
import axios from 'axios'
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'

const api = import.meta.env.VITE_BASE_URL
const router = useRouter()

// 驗證 + 取得代辦事項
const token = document.cookie.replace(/(?:^|.*;\s*)TodoToken\s*=\s*([^;]*).*$/i, '$1')
onMounted(async () => {
  try {
    await axios.get(`${api}users/checkout`, {
      headers: {
        Authorization: token,
      },
    })
    getTodo()
  } catch (error) {
    console.log('錯誤', error)
    router.push('/')
  }
})

// 取得Todo
const todoList = ref([])
const getTodo = async () => {
  try {
    const res = await axios.get(`${api}todos/`, {
      headers: {
        Authorization: token,
      },
    })
    todoList.value = res.data.data.map((todo) => ({
      ...todo,
      isEdit: false,
    }))
  } catch (error) {
    console.log(error)
  }
}

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
  } catch (error) {
    console.log(error)
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
  } catch (error) {
    console.log(error)
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
    console.log(todo.status);
  } catch (error) {
    console.log(error)
  }
}

</script>

<template>
  <div id="todoListPage" class="bg-half">
    <TheHeader :token="token"/>
    <div class="container todoListPage vhContainer">
      <div class="todoList_Content">
        <div class="inputBox">
          <input type="text" placeholder="請輸入待辦事項" v-model="todoContent.content"/>
          <a href="#" @click.prevent="addTodo(todoContent)">
            <i class="bi bi-plus-lg"></i>
          </a>
        </div>
        <div class="todoList_list">
          <ul class="todoList_tab">
            <li><a href="#" class="active">全部</a></li>
            <li><a href="#">待完成</a></li>
            <li><a href="#">已完成</a></li>
          </ul>
          <div class="todoList_items">
            <ul class="todoList_item">
              <li v-for="todo in todoList" :key="todo.id">
                <label class="todoList_label">
                  <input class="todoList_input" type="checkbox" @click="toggleTodo(todo)" :disabled="todo.status === true"/>
                  <span>{{todo.content}}</span>
                </label>
                <button type="button" class="btn" @click.prevent="deleteTodo(todo.id)">
                  <i class="bi bi-x-lg"></i>
                </button>
              </li>
            </ul>
            <div class="todoList_statistics">
              <p>5 個已完成項目</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
