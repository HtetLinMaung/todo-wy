<template>
  <div id="app">
    <Todo :todos="todos"/>
  </div>
</template>

<script>
import Todo from './components/todo.vue'
import axios from 'axios'
 
export default {
  name: 'app',
  components: {
    Todo
  },
  data(){
    return {
      todos: [],
    }
  },  

  mounted(){
    fetch("http://localhost:8000/todo/")
    .then(response => response.json())
    .then((data) => {
      this.todos = data;
    })
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
