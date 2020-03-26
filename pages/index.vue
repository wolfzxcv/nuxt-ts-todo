<template>
  <div class="container border col-4 mt-5">
    <form class="m-3" @submit.prevent="addTodo">
      <input
        v-model="title"
        type="text"
        placeholder="add something.."
        required
      />
      <button class="btn btn-primary btn-sm">ADD</button>
    </form>
    <div class="m-3">
      <button class="btn btn-info btn-sm" @click="showAll">All</button>
      <button class="btn btn-warning btn-sm mx-3" @click="showIncompleted">
        Incompleted
      </button>
      <button class="btn btn-secondary btn-sm" @click="showCompleted">
        Completed
      </button>
    </div>
    <div v-for="todo in displayTodos" :key="todo.id" class="m-3">
      <button
        class="btn btn-outline-danger btn-sm"
        @click="deleteTodo(todo.id)"
      >
        <i class="fa fa-trash-alt" aria-hidden="true"></i>
      </button>
      {{ todo.title }}
      <button
        class="btn btn-outline-success btn-sm"
        @click="markComplete(todo.id)"
      >
        <span v-if="todo.completed">V</span>
        <span v-else>--</span>
      </button>
    </div>
  </div>
</template>

<script lang="ts">
import { Vue, Component } from 'vue-property-decorator'
import * as uuid from 'uuid'

interface ITodo {
  id: string
  title: string
  completed: boolean
}

@Component
export default class extends Vue {
  title: string = ''
  todos: ITodo[] = []
  displayTodos: ITodo[] = []

  mounted() {
    this.todos = JSON.parse(localStorage.getItem('nuxt-todo') as any) || []
    this.displayTodos = this.todos
  }

  storeInLocalStorage() {
    localStorage.setItem('nuxt-todo', JSON.stringify(this.todos))
  }

  addTodo() {
    const newTodo: ITodo = {
      id: uuid.v4(),
      title: this.title,
      completed: false
    }
    this.todos = [...this.todos, newTodo]
    this.title = ''
    this.showAll()
    this.storeInLocalStorage()
  }

  deleteTodo(id: string) {
    this.todos = this.todos.filter((todo: ITodo) => todo.id !== id)
    this.showAll()
    this.storeInLocalStorage()
  }

  markComplete(id: string) {
    this.todos = this.todos.map((todo: ITodo) => {
      if (todo.id === id) {
        todo.completed = !todo.completed
      }
      return todo
    })
    this.showAll()
    this.storeInLocalStorage()
  }

  showAll() {
    this.displayTodos = this.todos
  }

  showIncompleted() {
    this.displayTodos = this.todos.filter(
      (todo: ITodo) => todo.completed === false
    )
  }

  showCompleted() {
    this.displayTodos = this.todos.filter(
      (todo: ITodo) => todo.completed === true
    )
  }
}
</script>
