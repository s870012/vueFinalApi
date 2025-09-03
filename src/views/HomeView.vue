<script setup>
import axios from 'axios'
import MessageToast from '@/components/MessageToast.vue'

import { ref, watch, provide } from 'vue'
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

// 登入、註冊切換
const isRegister = ref(false)
const toggleRegister = () => {
  if (isRegister.value === false) {
    isRegister.value = true
  } else {
    isRegister.value = false
  }
}

// 登入
const loginField = ref({
  email: '',
  password: '',
})

const errorLoginField = ref({
  email: '',
  password: '',
})

const loginRes = ref('')
const handleLogin = async (loginField) => {
  try {
    const res = await axios.post(`${api}users/sign_in`, loginField)
    loginRes.value = res.data.token
    document.cookie = `TodoToken=${res.data.token};path=/`
    router.push('/todo')
  } catch (error) {
    console.log('錯誤', error)
    createMessage('登入失敗', false)
  }
}

// 登入監聽
watch(
  loginField,
  (newLogin) => {
    const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
    if (!emailPattern.test(newLogin.email)) {
      errorLoginField.value.email = '請輸入正確的 E-mail 格式'
    } else {
      errorLoginField.value.email = ''
    }
  },

  { deep: true },
)

// 註冊
const registerField = ref({
  email: '',
  password: '',
  nickname: '',
})

const confirmField = ref({
  password: '',
})

const handleRegister = async (registerField) => {
  try {
    if (registerField.password === confirmField.value.password) {
      await axios.post(`${api}users/sign_up`, registerField)
      isRegister.value = false
      createMessage('註冊成功', true)
    } else {
      alert('密碼輸入不一樣')
    }
  } catch (error) {
    console.log('錯誤', error)
    createMessage(error.response.data.message, false)
  }
}

// 註冊監聽
const errorRegisField = ref({
  email: '',
  password: '',
  nickname: '',
})

const errorConfirmField = ref({
  password: '',
})

watch(
  registerField,
  (newRegis) => {
    const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
    if (!emailPattern.test(newRegis.email)) {
      errorRegisField.value.email = '請輸入正確的 E-mail 格式'
    } else {
      errorRegisField.value.email = ''
    }

    if (newRegis.nickname.length < 2) {
      errorRegisField.value.nickname = '暱稱請至少需 2 個字'
    } else {
      errorRegisField.value.nickname = ''
    }

    if (newRegis.password.length < 6) {
      errorRegisField.value.password = '密碼至少需 6 碼'
    } else {
      errorRegisField.value.password = ''
    }
  },

  { deep: true },
)

watch(
  confirmField,
  (newConfirm) => {
    console.log(newConfirm.password, registerField.value.password)

    if (newConfirm.password !== registerField.value.password) {
      errorConfirmField.value.password = '請輸入相同密碼'
    } else if (!newConfirm.value.password || !newConfirm.value) {
      errorConfirmField.value.password = ''
    } else {
      errorConfirmField.value.password = ''
    }
  },
  { deep: true },
)
</script>

<template>
  <MessageToast />
  <div id="loginPage" class="bg-yellow">
    <div class="container loginPage vhContainer">
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
      <!-- 註冊 -->
      <div v-if="isRegister">
        <form class="formControls" action="submit">
          <h2 class="formControls_txt">註冊帳號</h2>
          <label class="formControls_label" for="email">Email</label>
          <input
            class="formControls_input"
            type="text"
            name="email"
            placeholder="請輸入 email"
            v-model="registerField.email"
            required
          />
          <span v-if="errorRegisField.email">{{ errorRegisField.email }}</span>
          <label class="formControls_label" for="name">您的暱稱</label>
          <input
            class="formControls_input"
            type="text"
            name="name"
            placeholder="請輸入您的暱稱"
            v-model="registerField.nickname"
          />
          <span v-if="errorRegisField.nickname">{{ errorRegisField.nickname }}</span>
          <label class="formControls_label" for="pwd">密碼</label>
          <input
            class="formControls_input"
            type="password"
            name="pwd"
            placeholder="請輸入密碼"
            v-model="registerField.password"
            required
          />
          <span v-if="errorRegisField.password">{{ errorRegisField.password }}</span>
          <label class="formControls_label" for="pwd">再次輸入密碼</label>
          <input
            class="formControls_input"
            type="password"
            name="pwd"
            placeholder="請再次輸入密碼"
            v-model="confirmField.password"
            required
          />
          <span v-if="errorConfirmField.confirmPassword">{{
            errorConfirmField.confirmPassword
          }}</span>
          <input
            class="formControls_btnSubmit"
            type="button"
            @click="handleRegister(registerField)"
            value="註冊帳號"
          />
          <a class="formControls_btnLink" href="#" @click.prevent="toggleRegister">登入</a>
        </form>
      </div>
      <!-- 登入 -->
      <div v-else>
        <form class="formControls" action="index.html">
          <h2 class="formControls_txt">最實用的線上代辦事項服務</h2>
          <label class="formControls_label" for="email">Email</label>
          <input
            class="formControls_input"
            type="text"
            name="email"
            placeholder="請輸入 email"
            v-model="loginField.email"
            required
          />
          <span v-if="errorLoginField.email">{{ errorLoginField.email }}</span>
          <label class="formControls_label" for="pwd">密碼</label>
          <input
            class="formControls_input"
            type="password"
            name="pwd"
            placeholder="請輸入密碼"
            required
            v-model="loginField.password"
          />
          <button class="formControls_btnSubmit" type="button" @click="handleLogin(loginField)">
            登入
          </button>
          <a class="formControls_btnLink" href="#" @click.prevent="toggleRegister">註冊帳號</a>
        </form>
      </div>
    </div>
  </div>
</template>
