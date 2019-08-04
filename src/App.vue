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
      todos: []
      // todos: [
      //   {title: "onetodo1", detail: "todo list One", done: false},
      //   {title: "towtodo2", detail: "todo list Two", done: false},
      //   {title: "threetodo3", detail: "todo list Three", done: true},
      // ]
    }
  },  
  // mounted () {
  //   axios.get('http://localhost:8000/todo/')
  //       .then(res => {
  //         this.todos = res;
  //       console.log(todos);
  //     })
  //     .catch(err => {
  //       console.log(err);
  //     });
  // }
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
