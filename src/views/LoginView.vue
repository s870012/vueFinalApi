<script setup>
import axios from 'axios'

import { RouterLink, useRouter } from 'vue-router'
import { ref, watch } from 'vue'

const api = import.meta.env.VITE_BASE_URL
const router = useRouter()

const loginField = ref({
  email: '',
  password: '',
})

const loginRes = ref('')
const handleLogin = async (signInField) => {
  try {
    const res = await axios.post(`${api}users/sign_in`, signInField)
    loginRes.value = res.data.token
    document.cookie = `TodoToken=${res.data.token};path=/`
    router.push('/todo')
  } catch (error) {
    console.log('錯誤', error)
    alert('登入失敗')
  }
}
</script>

<template>
  <div id="loginPage" class="bg-yellow">
    <div class="conatiner loginPage vhContainer">
      <div class="side">
        <a href="#"
          ><img
            class="logoImg"
            src="https://raw.githubusercontent.com/hexschool/2022-web-layout-training/main/todolist/logo.png"
            alt=""
        /></a>
        <img
          class="d-m-n"
          src="https://raw.githubusercontent.com/hexschool/2022-web-layout-training/main/todolist/img.png"
          alt="workImg"
        />
      </div>
      <div>
        <form class="formControls" action="index.html">
          <h2 class="formControls_txt">最實用的線上代辦事項服務</h2>
          <label class="formControls_label" for="email">Email</label>
          <input
            class="formControls_input"
            type="text"
            id="email"
            name="email"
            placeholder="請輸入 email"
            v-model="loginField.email"
            required
          />
          <span>此欄位不可留空</span>
          <label class="formControls_label" for="pwd">密碼</label>
          <input
            class="formControls_input"
            type="password"
            name="pwd"
            id="pwd"
            placeholder="請輸入密碼"
            required
            v-model="loginField.email"
          />
          <button
            class="formControls_btnSubmit"
            type="button"
            onclick="javascript:location.href='#todoListPage'"
            @click="handleLogin(loginField)"
          >
            登入
          </button>
          <RouterLink class="formControls_btnLink" to="/register">註冊帳號</RouterLink>
        </form>
      </div>
    </div>
  </div>
</template>
