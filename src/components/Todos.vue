<template>
  <section class="todoapp">
    <header class="header">
      <h1>Todos</h1>
      <input type="text" class="new-todo" placeholder="Ajouter une tâche" v-model="newTodo" @keyup.enter="addTodo">
    </header>
    <div class="main">
      <input type="checkbox" class="toggle-all" name="toggle-all" id="toggle-all" v-model="allDone">
      <label for="toggle-all">tous cocher</label>
      <ul class="todo-list">
        <li class="todo" v-for="todo in filteredTodos" :key="todo.id" :class="{completed: todo.completed, editing: todo === editing}">
          <div class="view">
            <input type="checkbox" v-model="todo.completed" class="toggle">
            <label @dblclick="editTodo(todo)">{{ todo.name }}</label>
            <button class="destroy" @click="deleteTodo(todo)"></button>
          </div>
          <input type="text" class="edit" v-model="todo.name" @keyup.enter="doneEditTodo" @blur="doneEditTodo" @keyup.esc="cancelEditTodo" v-focus="todo === editing">
        </li>
      </ul>
    </div>
    <footer class="footer" v-show="hasTodo">
      <span class="todo-count"><strong>{{ remaining }}</strong> tâches à faire</span>
      <ul class="filters">
        <li><a href="#" :class="{selected: filter === 'all'}" @click.prevent="filter = 'all'">Toutes</a></li>
        <li><a href="#" :class="{selected: filter === 'todo'}" @click.prevent="filter = 'todo'">A faire</a></li>
        <li><a href="#" :class="{selected: filter === 'done'}" @click.prevent="filter = 'done'">Complétés</a></li>
      </ul>
      <button class="clear-completed" @click.prevent="deleteCompleted" v-show="hasTodoCompleted">Supprimer les tâches complétés</button>
    </footer>
  </section>
</template>

<script>
import Vue from 'vue'
export default {
  data(){
    return {
      todos: [],
      oldTodo: '',
      newTodo: '',
      filter: 'all',
      editing: null
    }
  },
  methods:{
    addTodo (){
      this.todos.push({
        completed: false,
        name: this.newTodo
      })
      this.newTodo = ''
    },
    deleteTodo(todo){
      this.todos = this.todos.filter(i => i !== todo)
    },
    deleteCompleted(){
      this.todos = this.todos.filter(todo => !todo.completed)
    },
    editTodo (todo){
      this.oldTodo = todo.name
      this.editing = todo
    },
    doneEditTodo(){
      this.editing = null
    },
    cancelEditTodo(){
      this.editing.name = this.oldTodo
      this.doneEditTodo()
    }
  },
  computed:{
    allDone: {
      get (){
        return this.remaining === 0
      },
      set(value){
        this.todos.forEach(todo => {
          todo.completed = value
        })
      }
    },
    remaining (){
      return this.todos.filter(todo => !todo.completed).length
    },
    hasTodoCompleted (){
      return this.todos.filter(todo => todo.completed).length
    },
    hasTodo(){
      return this.todos.length > 0
    },
    filteredTodos(){
      if(this.filter === 'todo'){
        return this.todos.filter(todo => !todo.completed)
      } else if (this.filter === 'done'){
        return this.todos.filter(todo => todo.completed)
      }
      return this.todos
    }
  },
  directives:{
    focus (el, val){
      if(val){
        Vue.nextTick(_ =>{
          el.focus()
        })
      }
    }
  }
}
</script>

<style src="./todos.css"></style>