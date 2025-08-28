<script setup>
import { ref } from 'vue'
import axios from 'axios'
import { useRouter, RouterLink } from 'vue-router'

const router = useRouter()
const api_url = 'https://todolist-api.hexschool.io'

const user = ref({
  email: '',
  password: '',
})

const login = async () => {
  try {
    const res = await axios.post(`${api_url}/users/sign_in`, user.value)
    document.cookie = `token=${res.data.token};`
    router.push('/')
  } catch (error) {
    console.log(error)
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
        <form class="formControls">
          <h2 class="formControls_txt">最實用的線上代辦事項服務</h2>
          <label class="formControls_label" for="email">Email</label>
          <input
            class="formControls_input"
            type="text"
            id="email"
            name="email"
            placeholder="請輸入 email"
            required
            v-model="user.email"
          />
          <span v-if="!user.email">此欄位不可留空</span>
          <label class="formControls_label" for="pwd">密碼</label>
          <input
            class="formControls_input"
            type="password"
            name="password"
            id="password"
            placeholder="請輸入密碼"
            required
            v-model="user.password"
          />
          <input class="formControls_btnSubmit" type="button" @click="login" value="登入" />
          <RouterLink to="/register" class="formControls_btnLink">註冊帳號</RouterLink>
        </form>
      </div>
    </div>
  </div>
</template>
