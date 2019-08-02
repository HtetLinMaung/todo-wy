<template>
  <div id="app">
    <Todo :todos="todos"/>
  </div>
</template>

<script>
import Todo from './components/todo.vue'

export default {
  name: 'app',
  components: {
    Todo
  },
  data(){
    return {
      todos: [
        {title: "onetodo1", detail: "todo list One", done: false},
        {title: "towtodo2", detail: "todo list Two", done: false},
        {title: "threetodo3", detail: "todo list Three", done: true},
      ]
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
