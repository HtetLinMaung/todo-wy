<template>
<b-container>
    <b-row class="mb-4">
        <b-col>
            <b-button :class="showAddNew ? 'collapsed' : null" :aria-expanded="showAddNew ? 'true' : 'false'" aria-controls="collapse-4" @click="showAddNew = !showAddNew" variant="primary">
            Add New +
            </b-button>
            <b-collapse id="collapse-4" v-model="showAddNew" class="mt-2">
                <b-card>
                    <b-form v-if="temp == null" >
                    <b-form-group>
                        <b-form-input v-model="title" placeholder="Title"></b-form-input>
                    </b-form-group>
                    <b-form-group>
                        <b-form-textarea v-model="description" placeholder="Description" class="mb-2"></b-form-textarea>
                    </b-form-group>
                    <b-button @click="addtodo()" variant="info" class="px-4 mr-2">Add</b-button>
                    <b-button type="reset" variant="danger" class="px-4">Clear</b-button>
                    </b-form>
                </b-card>
            </b-collapse>
            <b-collapse id="collapse-4" v-model="showEdit" class="mt-2">
                <b-card>
                    <b-form v-if="temp == null" >
                    <b-form-group>
                        <b-form-input v-model="todoedit.title"></b-form-input>
                    </b-form-group>
                    <b-form-group>
                        <b-form-textarea v-model="todoedit.description" placeholder="New Description" class="mb-2"></b-form-textarea>
                    </b-form-group>
                    <b-button @click="updateTodo(todoedit)" variant="info" class="px-4 mr-2">Update</b-button>
                    <b-button @click="showEdit = !showEdit" type="reset" variant="danger" class="px-4">x</b-button>
                    </b-form>
                </b-card>
            </b-collapse>
        </b-col>
    </b-row>
    <b-row>
        <b-col v-for="(todo, index) in todos" :key="index" md=4 class="mb-4">
            <b-card title="Title" header-tag="header" footer-tag="footer">
                <h6 slot="header" class="mb-0">{{todo.title}}</h6>
                <b-card-text>{{todo.description}}</b-card-text>
                <b-button :class="showEdit ? 'collapsed' : null" :aria-expanded="showEdit ? 'true' : 'false'" aria-controls="collapse-4" @click="showEdit = !showEdit, todoedit = todo" variant="primary">
                Toggle Collapse
                </b-button>
                <b-button @click="deleteTodo(todo.id, index)" variant="danger">delete</b-button>
            </b-card>
        </b-col>
    </b-row>
</b-container>
</template>

<script>
import axios from 'axios'
export default {
    props: ['todos'],
    data() {
        return {
            search: "",
            temp: null,
            title: null,
            description: null,
            editTodo: null,
            showAddNew: false,
            showEdit: false,
            todoedit: "",
        }
    },
    methods: {
        deleteTodo(id, index) {
            console.log(index, id)
            fetch("http://localhost:8000/todo/" + id, {
                method: "DELETE"
            })
            .then(() => {
                this.todos.splice(index, 1);
            })
        }, 
        // updateTodo(todo){
        //     console.log(todo)
        //     fetch("http://localhost:8000/todo/" + todo.id, {
        //         body: JSON.stringify(todo),
        //         method: "PUT",
        //         headers: {
        //             "Content-Type": "application/json"
        //         }
        //     })
        //     .then(() => {
        //         this.editTodo = null;
        //     })
        // },
        updateTodo(todoedit){
            axios
            .put(`http://localhost:8000/todo/${todoedit.id}/`,{
                title: todoedit.title,
                description: todoedit.description,
            })
            .then(res => {
                console.log(todoedit)
            })
            // .catch(err => {
            //     console.log(err);
            // });
        },
        addtodo() {
            axios
            .post("http://localhost:8000/todo/", {
                title: this.title,
                description: this.description,
            })
            .then(() => {
                const title = this.title;
                const description = this.description;
                this.todos.push({
                    title,
                    description,
                });
            })
            .catch(err => {
                console.log(err);
            });
        }
        // addtodo() {
        //     fetch("http://localhost:8000/todo/", {  
        //         method: "POST"
        //     })
        //     const title = this.title;
        //         const description = this.description;
        //         this.todos.push({
        //             title,
        //             description,
        //         });
        // }, 
        // addtodo() {
        //     if(this.title.length > 0 && this.description.length > 0){
        //         const title = this.title;
        //         const description = this.description;
        //         this.todos.push({
        //             title,
        //             description,
        //             done: false,
        //         });
        //     }
        // },
        // complete(todo){
        //     const todoIndex = this.todos.indexOf(todo);
        //     console.log(this.todos.indexOf(todo))
        //     this.todos[todoIndex].done = true;
        //     alert("success!")
        // }
    },
}
</script>
