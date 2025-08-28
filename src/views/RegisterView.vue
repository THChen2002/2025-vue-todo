<script setup>
import { ref } from 'vue'
import axios from 'axios'
import { useRouter, RouterLink } from 'vue-router'

const router = useRouter()
const api_url = 'https://todolist-api.hexschool.io'

const registerFields = ref({
  email: '',
  nickname: '',
  password: '',
  password2: '',
})
const errorMessage = ref('')

const register = async () => {
  if (registerFields.value.password !== registerFields.value.password2) {
    errorMessage.value = '密碼不一致'
    return
  }
  try {
    await axios.post(`${api_url}/users/sign_up`, registerFields.value)
    router.push('/login')
    errorMessage.value = ''
  } catch (error) {
    registerFields.value.email = ''
    registerFields.value.nickname = ''
    registerFields.value.password = ''
    registerFields.value.password2 = ''
    errorMessage.value = error.response.data.message
  }
}
</script>
<template>
  <div id="signUpPage" class="bg-yellow">
    <div class="conatiner signUpPage vhContainer">
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
        <form class="formControls">
          <h2 class="formControls_txt">註冊帳號</h2>
          <label class="formControls_label" for="email">Email</label>
          <input
            class="formControls_input"
            type="text"
            id="email"
            name="email"
            placeholder="請輸入 email"
            required
            v-model="registerFields.email"
          />
          <label class="formControls_label" for="name">您的暱稱</label>
          <input
            class="formControls_input"
            type="text"
            name="name"
            id="name"
            placeholder="請輸入您的暱稱"
            v-model="registerFields.nickname"
          />
          <label class="formControls_label" for="password">密碼</label>
          <input
            class="formControls_input"
            type="password"
            name="password"
            id="password"
            placeholder="請輸入密碼"
            required
            v-model="registerFields.password"
          />
          <label class="formControls_label" for="password2">再次輸入密碼</label>
          <input
            class="formControls_input"
            type="password"
            name="password2"
            id="password2"
            placeholder="請再次輸入密碼"
            required
            v-model="registerFields.password2"
          />
          <span>{{ errorMessage }}</span>
          <input class="formControls_btnSubmit" type="button" @click="register" value="註冊帳號" />
          <RouterLink to="/login" class="formControls_btnLink">登入</RouterLink>
        </form>
      </div>
    </div>
  </div>
</template>
