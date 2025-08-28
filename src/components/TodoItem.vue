<script setup>
const props = defineProps({
  todoList: {
    type: Array,
    required: true,
  },
})

const emit = defineEmits(['deleteTodo', 'updateTodo'])

const deleteTodo = (id) => {
  emit('deleteTodo', id)
}

const updateTodo = (id) => {
  emit('updateTodo', id)
}
</script>

<template>
  <ul class="todoList_item">
    <li v-for="todo in todoList" :key="todo.id">
      <label class="todoList_label">
        <input
          class="todoList_input"
          type="checkbox"
          :checked="todo.status"
          @change="updateTodo(todo.id)"
        />
        <span>{{ todo.content }}</span>
      </label>
      <a href="#" @click="deleteTodo(todo.id)">
        <i class="fa fa-times"></i>
      </a>
    </li>
  </ul>
</template>
