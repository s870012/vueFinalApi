<script setup>
import axios from 'axios'
import { RouterLink, useRouter } from 'vue-router'

const api = import.meta.env.VITE_BASE_URL
const router = useRouter()
const props = defineProps({
  token: { type: String },
  nickname: { type: String },
})

// 登出
const signOutTodo = async () => {
  try {
    await axios.post(`${api}users/sign_out`, null, {
      headers: {
        Authorization: props.token,
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
  <header>
    <nav>
      <h1><RouterLink to="/">ONLINE TODO LIST</RouterLink></h1>
      <ul>
        <li class="todo_sm">
          <RouterLink to="/todo"
            ><span>{{ props.nickname }} 的代辦</span></RouterLink
          >
        </li>
        <li><a href="#loginPage" @click.prevent="signOutTodo">登出</a></li>
      </ul>
    </nav>
  </header>
</template>
