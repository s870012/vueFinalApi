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
    router.push('/signup')
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

// editTodo
const tempTodo = ref({
  content: '',
})
const toggleEdit = (todo) => {
  const index = todoList.value.findIndex((i) => i.id === todo.id)
  todoList.value[index].isEdit = true
  tempTodo.value = todo.content
}

const editTodo = async (todo) => {
  const index = todoList.value.findIndex((i) => i.id === todo.id)
  todoList.value[index].content = tempTodo.value

  try {
    await axios.put(`${api}todos/${todo.id}`, todo, {
      headers: {
        Authorization: token,
      },
    })
    getTodo()
  } catch (error) {
    console.log(error)
  }
}

const closeEdit = (id) => {
  const index = todoList.value.findIndex((i) => i.id === id)
  todoList.value[index].isEdit = false
}

// todo status
const toggleTodo = async (todo) => {
  try {
    await axios.patch(`${api}todos/${todo.id}/toggle`, null, {
      headers: {
        Authorization: token,
      },
    })
    console.log('代辦事項完成')
  } catch (error) {
    console.log(error)
  }
}

// 登出
const signOutTodo = async () => {
  try {
    await axios.post(`${api}users/sign_out`, null, {
      headers: {
        Authorization: token,
      },
    })
    document.cookie = 'TodoToken=; expires=Thu, 01 Jan 1970 00:00:00 GMT; path=/'
    router.push('/')
  } catch (error) {
    console.log(error)
  }
}
</script>

<template>
  <div id="todoListPage" class="bg-half">
    <TheHeader />
    <div class="container todoListPage vhContainer">
      <div class="todoList_Content">
        <div class="inputBox">
          <input type="text" placeholder="請輸入待辦事項" />
          <a href="#">
            <i class="fa fa-plus"></i>
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
              <li>
                <label class="todoList_label">
                  <input class="todoList_input" type="checkbox" value="true" />
                  <span>把冰箱發霉的檸檬拿去丟</span>
                </label>
                <a href="#">
                  <i class="fa fa-times"></i>
                </a>
              </li>
              <li>
                <label class="todoList_label">
                  <input class="todoList_input" type="checkbox" value="true" />
                  <span>打電話叫媽媽匯款給我</span>
                </label>
                <a href="#">
                  <i class="fa fa-times"></i>
                </a>
              </li>
              <li>
                <label class="todoList_label">
                  <input class="todoList_input" type="checkbox" value="true" />
                  <span>整理電腦資料夾</span>
                </label>
                <a href="#">
                  <i class="fa fa-times"></i>
                </a>
              </li>
              <li>
                <label class="todoList_label">
                  <input class="todoList_input" type="checkbox" value="true" />
                  <span>繳電費水費瓦斯費</span>
                </label>
                <a href="#">
                  <i class="fa fa-times"></i>
                </a>
              </li>
              <li>
                <label class="todoList_label">
                  <input class="todoList_input" type="checkbox" value="true" />
                  <span>約vicky禮拜三泡溫泉</span>
                </label>
                <a href="#">
                  <i class="fa fa-times"></i>
                </a>
              </li>
              <li>
                <label class="todoList_label">
                  <input class="todoList_input" type="checkbox" value="true" />
                  <span>約ada禮拜四吃晚餐</span>
                </label>
                <a href="#">
                  <i class="fa fa-times"></i>
                </a>
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
