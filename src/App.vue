<template class="d-flex justify-content-center">
  <h1>Todo List</h1>
  <div class="container d-flex justify-content-center">
    <div class="col-11 col-sm-10 col-md-8 col-lg-6">
      <h3 v-if="todos === null">Loading...</h3>
      <TodoList
        v-else
        :todos="todos"
        :updateCompleted="updateCompleted"
        :editTodo="editTodo"
        :removeTodo="removeTodo"
      />
      <br v-show="isUpdating" />
      <form @submit="handleSubmit">
        <div class="form-group text-start">
          <small v-if="isUpdating" class="form-tex text-muted">Updating a todo....</small>
          <label for="title" />
          <input class="form-control" placeholder="Enter a new todo..." v-model="inputValue" />
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import TodoList from "./components/TodoList"
import axios from "axios"

export default {
  name: 'App',
  components: {
    TodoList
  },
  data() {
    return {
      todos: null,
      inputValue: "",
      isUpdating: false,
      currentTodo: null
    }
  },
  mounted() {
    axios.get("https://arcane-hollows-66380.herokuapp.com/todos")
    .then(({ data }) => this.todos = data)
  },
  methods: {
    addTodo() {
      const data = {
        username: "Thomas",
        title: this.inputValue
      }
      fetch(`https://arcane-hollows-66380.herokuapp.com/todos`, {
        method: "POST",
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(data)
      })
    },
    handleSubmit(e) {
      e.preventDefault()
      if (this.inputValue === "") return
      if (this.isUpdating) {
        this.updateTodo()
      } else {
        this.addTodo()
        this.todos.push({
          title: this.inputValue,
          completed: false
        })
      }
      this.inputValue = ""
    },
    updateTodo() {
      const data = { title: this.inputValue }
      axios.post(`https://arcane-hollows-66380.herokuapp.com/todos/${this.currentTodo.id}`, data)
      this.currentTodo.title = this.inputValue
      this.isUpdating = false
      this.currentTodo = null
      this.inputValue = ""
    },
    updateCompleted(id, completed) {
      const data = { completed }
      axios.put(`https://arcane-hollows-66380.herokuapp.com/todos/${id}`, data)
    },
    editTodo(todo) {
      console.log(todo)
      this.isUpdating = true
      this.currentTodo = todo
      this.inputValue = todo.title
    },
    removeTodo(id) {
      if (confirm("Are you sure?")) {
        const targetIndex = this.todos.findIndex(todo => +todo.id === +id)
        this.todos.splice(targetIndex, 1)
        axios.delete(`https://arcane-hollows-66380.herokuapp.com/todos/${id}`)
        this.isUpdating = false
        this.currentTodo = null
        this.inputValue = ""
      } else return
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
input[type='checkbox'] {
  margin: 0 .25rem 0 .5rem;
}
.done {
  text-decoration: line-through;
}
.todo-container {
  width: 85%
}
.button-container {
  width: 15%;
  min-width: 80px
}
</style>