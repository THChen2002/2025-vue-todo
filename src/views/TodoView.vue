<script setup>
import { ref, onMounted, computed } from 'vue'
import { useRouter } from 'vue-router'
import axios from 'axios'
import TodoItem from '@/components/TodoItem.vue'

const router = useRouter()
const api_url = 'https://todolist-api.hexschool.io'
const token =
  document.cookie
    ?.split('; ')
    .find((row) => row.startsWith('token='))
    ?.split('=')[1] || ''

const user = ref({})
const todoList = ref([])
const todo = ref('')
const activeTab = ref('all')

const filteredTodoList = computed(() => {
  switch (activeTab.value) {
    case 'pending':
      return todoList.value.filter((todo) => !todo.status)
    case 'completed':
      return todoList.value.filter((todo) => todo.status)
    default:
      return todoList.value
  }
})

const pendingTodo = computed(() => todoList.value.filter((todo) => !todo.status).length)

onMounted(async () => {
  try {
    const userRes = await axios.get(`${api_url}/users/checkout`, {
      headers: {
        Authorization: token,
      },
    })
    user.value = userRes.data
    const res = await axios.get(`${api_url}/todos`, {
      headers: {
        Authorization: token,
      },
    })
    todoList.value = res.data.data
  } catch (error) {
    router.push('/login')
  }
})

const logout = async () => {
  try {
    await axios.post(
      `${api_url}/users/sign_out`,
      {},
      {
        headers: {
          Authorization: token,
        },
      },
    )
  } catch (error) {
    console.log('後端登出失敗，但仍繼續前端登出:', error)
  }

  document.cookie = 'token=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/;'

  user.value = {}
  todoList.value = []

  router.push('/login')
}

const addTodo = async () => {
  try {
    const res = await axios.post(
      `${api_url}/todos`,
      { content: todo.value },
      {
        headers: {
          Authorization: token,
        },
      },
    )
    todoList.value.push(res.data.newTodo)
    todo.value = ''
  } catch (error) {
    console.log(error)
  }
}

const updateTodo = async (id) => {
  try {
    await axios.patch(
      `${api_url}/todos/${id}/toggle`,
      {},
      {
        headers: {
          Authorization: token,
        },
      },
    )
    todoList.value = todoList.value.map((todo) => {
      if (todo.id === id) {
        todo.status = !todo.status
      }
      return todo
    })
  } catch (error) {
    console.log(error)
  }
}

const deleteTodo = async (id) => {
  try {
    await axios.delete(`${api_url}/todos/${id}`, {
      headers: {
        Authorization: token,
      },
    })
    todoList.value = todoList.value.filter((todo) => todo.id !== id)
  } catch (error) {
    console.log(error)
  }
}

const switchTab = (tab) => {
  activeTab.value = tab
}
</script>

<template>
  <div id="todoListPage" class="bg-half">
    <nav>
      <h1><a href="#">ONLINE TODO LIST</a></h1>
      <ul>
        <li class="todo_sm">
          <a href="#"
            ><span>{{ user.nickname }}的代辦</span></a
          >
        </li>
        <li><a href="#" @click="logout">登出</a></li>
      </ul>
    </nav>
    <div class="conatiner todoListPage vhContainer">
      <div class="todoList_Content">
        <div class="inputBox">
          <input type="text" placeholder="請輸入待辦事項" v-model="todo" />
          <a href="#" @click.prevent="addTodo">
            <i class="fa fa-plus"></i>
          </a>
        </div>
        <div v-if="todoList.length > 0" class="todoList_list">
          <ul class="todoList_tab">
            <li>
              <a
                href="#"
                @click.prevent="switchTab('all')"
                :class="{ active: activeTab === 'all' }"
              >
                全部
              </a>
            </li>
            <li>
              <a
                href="#"
                @click.prevent="switchTab('pending')"
                :class="{ active: activeTab === 'pending' }"
              >
                待完成
              </a>
            </li>
            <li>
              <a
                href="#"
                @click.prevent="switchTab('completed')"
                :class="{ active: activeTab === 'completed' }"
              >
                已完成
              </a>
            </li>
          </ul>
          <div class="todoList_items">
            <TodoItem
              :todo-list="filteredTodoList"
              @delete-todo="deleteTodo"
              @update-todo="updateTodo"
            />
            <div class="todoList_statistics">
              <p>{{ pendingTodo }} 個待完成項目</p>
            </div>
          </div>
        </div>
        <div v-else class="text-center">
          <p>目前尚無待辦事項</p>
        </div>
      </div>
    </div>
  </div>
</template>
