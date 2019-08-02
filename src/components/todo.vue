<template>
    <div>
        <div v-if="temp == null" >
            <b-form-input v-model="title" placeholder="Title"></b-form-input>
            <b-form-input v-model="detail" placeholder="Detail"></b-form-input>
            <b-button @click="addtodo()">ADD</b-button>
        </div>
        <div v-for="(todo, index) in todos" :key="index">
            
            <div>
                Title: {{todo.title}}
                Description: {{todo.detail}}
                <button>edit</button>
                <button @click="deleteTodo(todo)">delete</button>
                <div v-if="todo.done == false" @click="complete(todo)"><font color="red">On Going</font></div>
                <div v-if="todo.done == true"><font color="green">Finished!</font></div>
                <br>
            </div>
        </div>
    </div>
</template>

<script>

export default {
    props: ['todos'],
    data() {
        return {
            search: "",
            temp: null,
        }
    },
    methods: {
        deleteTodo(todo) {
            console.log(todo)
        const todoIndex = this.todos.indexOf(todo);
        this.todos.splice(todoIndex, 1);
        },   
        addtodo() {
            const title = this.detail;
            const detail = this.detail;
            this.todos.push({
                title,
                detail,
                done: false,
            });
        },
        complete(todo){
            const todoIndex = this.todos.indexOf(todo);
            console.log(this.todos.indexOf(todo))
            this.todos[todoIndex].done = true;
            alert("success!")
        }
    },
}
</script>
