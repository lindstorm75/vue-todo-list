<template class="d-flex justify-content-center">
  <h1>Todo List</h1>
  <div class="container d-flex justify-content-center">
    <div class="col-11 col-sm-10 col-md-8 col-lg-6">
      <h3 v-if="todos === null">Loading...</h3>
      <ul v-else class="list-group">
        <li class="list-group-item d-flex" v-for="todo in todos">
          <span class="text-start" style="width: 85%">
            <input @change="updateCompleted(todo.id, todo.completed)" type="checkbox" v-model="todo.completed">
            <span v-if="todo.completed" class="done">{{ todo.title }}</span>
            <span v-else>{{ todo.title }}</span>
          </span>
          <div class="ms-auto" style="width: 15%; min-width: px">
            <button @click="editTodo(todo)" class="btn btn-sm btn-primary">Edit</button>
            <button @click="removeTodo(todo.id)" class="btn btn-sm btn-danger ms-2">x</button>
          </div>
        </li>
      </ul>
      <form @submit="handleSubmit" class="my-2">
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

export default {
  name: 'App',
  data() {
    return {
      todos: null,
      inputValue: "",
      updating: false,
      currentTodo: null
    }
  },
  mounted() {
    fetch("https://arcane-hollows-66380.herokuapp.com/todos")
    .then(res => res.json())
    .then(todos => this.todos = todos)
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
    updateCompleted(id, completed) {
      const data = { completed }
      fetch(`https://arcane-hollows-66380.herokuapp.com/todos/${id}`, {
        method: "PUT",
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(data)
      })
    },
    editTodo(todo) {
      this.isUpdating = true
      this.currentTodo = todo
      this.inputValue = todo.title
    },
    updateTodo() {
      const data = { title: this.inputValue }
      fetch(`https://arcane-hollows-66380.herokuapp.com/todos/${this.currentTodo.id}`, {
        method: "PUT",
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(data)
      })
      this.currentTodo.title = this.inputValue
      this.isUpdating = false
      this.currentTodo = null
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
    removeTodo(id) {
      if (confirm("Are you sure?")) {
        const targetIndex = this.todos.findIndex(todo => +todo.id === +id)
        this.todos.splice(targetIndex, 1)
        fetch(`https://arcane-hollows-66380.herokuapp.com/todos/${id}`, {
          method: "DELETE"
        })
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
</style>