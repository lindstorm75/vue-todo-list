<template>
  <div class="ms-auto button-container">
    <button @click="editTodo(todo)" class="btn btn-sm btn-primary">Edit</button>
    <button @click="removeTodo(todo.id)" class="btn btn-sm btn-danger ms-2">x</button>
  </div>
</template>

<script>
export default {
  name: "TodoButton",
  props: ["editTodo", "removeTodo"]
}
</script>

<style>

</style>