<template>
  <b-container>
    <div v-if="temp.length" class="text-left">
      <b-form-input v-model.lazy="search" id="input-small" size="md" placeholder="Search"></b-form-input>
    </div>
    <b-row class="mb-4">
      <b-col>
        <b-button
          :class="showAddNew ? 'collapsed' : null"
          :aria-expanded="showAddNew ? 'true' : 'false'"
          aria-controls="collapse-4"
          @click="showAddNew = !showAddNew"
          variant="primary"
        >Add New +</b-button>
        <b-collapse id="collapse-4" v-model="showAddNew" class="mt-2">
          <b-card>
            <b-form v-if="temp == null">
              <b-form-group>
                <b-form-input v-model="title" placeholder="Title"></b-form-input>
              </b-form-group>
              <b-form-group>
                <b-form-textarea v-model="description" placeholder="Description" class="mb-2"></b-form-textarea>
              </b-form-group>
              <b-button
                @click="addtodo(), showAddNew = !showAddNew"
                variant="info"
                class="px-4 mr-2"
              >Add</b-button>
              <b-button type="reset" variant="danger" class="px-4">Clear</b-button>
            </b-form>
          </b-card>
        </b-collapse>
      </b-col>
    </b-row>
    <b-row>
      <b-col v-for="(todo, index) in todos" :key="index" md="4" xs="12" class="mb-4">
        <b-card header-tag="header" footer-tag="footer">
          <h6 slot="header" class="mb-0">{{todo.title}}</h6>
          <b-card-text>{{todo.description}}</b-card-text>
          <b-button
            variant="primary"
            class="mr-1"
            id="show-btn"
            @click="$bvModal.show('bv-modal-example'), todoedit = todo"
          >Update</b-button>
          <b-button @click="deleteTodo(todo.id, index)" variant="danger">Delete</b-button>
        </b-card>
      </b-col>
    </b-row>

    <!-- Modal Box for Update Data -->
    <b-modal id="bv-modal-example" hide-footer>
      <b-form v-if="temp == null">
        <b-form-group>
          <b-form-input v-model="todoedit.title"></b-form-input>
        </b-form-group>
        <b-form-group>
          <b-form-textarea
            v-model="todoedit.description"
            placeholder="New Description"
            class="mb-2"
          ></b-form-textarea>
        </b-form-group>
        <b-button
          @click="updateTodo(todoedit), $bvModal.hide('bv-modal-example')"
          variant="info"
          class="px-4 mr-2"
        >Update</b-button>
        <b-button @click="showEdit = !showEdit" type="reset" variant="danger" class="px-4">x</b-button>
      </b-form>
    </b-modal>
  </b-container>
</template>

<script>
import axios from "axios";
export default {
  props: ["todos"],
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
      search: "",
      temp: null,
      collections: this.title || []
    };
  },
  methods: {
    deleteTodo(id, index) {
      console.log(index, id);
      fetch("http://localhost:8000/todo/" + id, {
        method: "DELETE"
      }).then(() => {
        this.todos.splice(index, 1);
      });
    },
    updateTodo(todoedit) {
      axios
        .put(`http://localhost:8000/todo/${todoedit.id}/`, {
          title: todoedit.title,
          description: todoedit.description
        })
        .then(res => {
          console.log(todoedit);
        })
        .catch(err => {
          console.log(err);
        });
    },
    addtodo() {
      axios
        .post("http://localhost:8000/todo/", {
          title: this.title,
          description: this.description
        })
        .then(() => {
          const title = this.title;
          const description = this.description;
          this.todos.push({
            title,
            description
          });
        })
        .catch(err => {
          console.log(err);
        });
    }
  },
  watch: {
    search: function(newVal) {
      if (!newVal) {
        this.todos = this.temp;
      } else {
        this.todos = this.todos.filter(todos =>
          todos.title.match(new RegExp(`^${newVal}`, "i"))
        );
      }
    }
  },
  mounted() {
    this.temp = this.todos;
  }
};
</script>
