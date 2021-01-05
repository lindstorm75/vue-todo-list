<template>
  <ul class="list-group">
    <li class="list-group-item d-flex align-items-center py-2" v-for="todo in todos" :key="todo.id">
      <span class="text-start todo-container">
        <input @change="updateCompleted(todo.id, todo.completed)" type="checkbox" v-model="todo.completed">
        <span v-if="todo.completed" class="done">{{ todo.title }}</span>
        <span v-else>{{ todo.title }}</span>
      </span>
      <TodoButton :editTodo="editTodo" :removeTodo="removeTodo" />
    </li>
  </ul>
</template>

<script>
import TodoButton from "./TodoButton"

export default {
  name: "TodoList",
  components: {
    TodoButton
  },
  props: ["todos", "updateCompleted", "editTodo", "removeTodo"]
}
</script>

<style>

</style>