<template>
    <div>
        <div v-if="temp == null" >
            <b-form-input v-model="search" placeholder="Search"></b-form-input>
        </div>
        <div v-for="todo in todos" :key="todo.title">
            
            <div>
                Title: {{todo.title}}
                Description: {{todo}}
                <button>edit</button>
                <button @click="deleteTodo(todo.title)">delete</button>
                <div v-if="todo.done == false"><font color="red">On Going</font></div>
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
        const todoIndex = this.title;
        this.todos.splice(todoIndex, 1);
        },
    },
}
</script>
