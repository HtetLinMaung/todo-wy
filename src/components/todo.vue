<template>
  <b-container>
    <b-row>
      <b-col class="mb-4">
        <div class="text-left">
          <b-form-input size="md" placeholder="Search" v-model="search"></b-form-input>
        </div>
      </b-col>
    </b-row>
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
          </b-card>
        </b-collapse>
      </b-col>
    </b-row>
    <b-row>
      <b-col
        v-for="(todo, index) in todos.slice(start, end)"
        :key="index"
        md="4"
        xs="12"
        class="mb-4"
      >
        <b-card header-tag="header" :class="{true:todo.done}" footer-tag="footer">
          <h6 slot="header" class="mb-0">
            <b-form-checkbox v-model="todo.done" switches @change="updateTodo(todo)">{{todo.title}}</b-form-checkbox>
          </h6>
          <b-card-text>{{todo.description}}</b-card-text>
          <b-button
            variant="primary"
            class="mr-1"
            id="show-btn"
            @click="$bvModal.show('bv-modal-example'), todoedit = todo"
          >Update</b-button>
          <b-button @click="deleteTodo(todo.id), paginate()" variant="danger">Delete</b-button>
        </b-card>
      </b-col>
    </b-row>
    <b-row v-show="todos.length > 6">
      <b-col md="12" class="my-1">
        <b-pagination
          @change="onPageChanged"
          :total-rows="totalRows || newtotalRows"
          :per-page="perPage"
          v-model="currentPage"
          class="my-0"
        />
      </b-col>
    </b-row>
    <!-- Modal Box for Update Data -->
    <b-modal id="bv-modal-example" hide-footer>
      <b-form>
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
  data() {
    return {
      origin_todos: null,
      todos: [],
      title: null,
      description: null,
      editTodo: null,
      showAddNew: false,
      showEdit: false,
      todoedit: "",
      search: "",
      done: "",
      start: 0,
      end: null,
      paginatedItems: null,
      currentPage: 2,
      perPage: 6,
      totalRows: null,
      todoItems: null,
      totalPage: 0,
      current_page: 0
    };
  },
  methods: {
    deleteTodo(id) {
      // let index = this.origin_todos.findIndex(todo => todo.id == id);
      // this.origin_todos.splice(index, 1);
      // index = this.todos.findIndex(todo => todo.id == id);
      // this.todos.splice(index, 1);
      let collections = [this.origin_todos, this.todos];
      collections = collections.map(collection => {
        return collection.splice(collection.findIndex(obj => obj.id == id), 1);
      });
      
      this.totalRows = this.todos.length;

      const last_page = Math.ceil(this.todos.length / this.perPage);
      if ((index + 1) % this.perPage == 1) {
        // if last item in current page
        this.resetPosition();
      }

      fetch(`http://localhost:8000/todo/${id}/`, {
        method: "DELETE"
      }).then(() => {});
    },
    updateTodo(todoedit) {
      axios
        .put(`http://localhost:8000/todo/${todoedit.id}/`, {
          title: todoedit.title,
          description: todoedit.description,
          done: !todoedit.done
        })
        .then(res => {
          // console.log(todoedit.done);
        })
        .catch(err => {
          console.log(err);
        });
    },
    addtodo() {
      this.origin_todos.unshift({
        title: this.title,
        description: this.description
      });

      if (
        !this.search ||
        (this.search &&
          this.origin_todos[0].title.match(new RegExp(`^${this.search}`, "i")))
      ) {
        this.todos.unshift({
          title: this.title,
          description: this.description
        });
        if (this.todos.length % 6 != 0) {
          this.totalRows = this.todos.length;
          this.resetPosition();
        }
      }

      axios
        .post("http://localhost:8000/todo/", {
          title: this.title,
          description: this.description
        })
        .then(res => {
          this.origin_todos[this.origin_todos.length - 1].id = res.data.id;
          this.todos[this.todos.length - 1].id = res.data.id;
        })
        .catch(err => {
          console.log(err);
        });
    },
    paginate(page_size, page_number) {
      // console.log("paginate=>",this.todos)
      let itemsToParse = this.todos;
      this.todoItems = itemsToParse.slice(
        page_number * page_size,
        (page_number + 1) * page_size
      );
      // console.log(page_size, page_number)
    },
    onPageChanged(page) {
      this.end = this.perPage * page;
      this.start = this.end - this.perPage;
    },
    resetPosition() {
      this.start = 0;
      this.end =
        this.todos.length < this.perPage ? this.todos.length : this.perPage;
      this.totalRows = this.todos.length;
    }
  },
  watch: {
    search: function(newVal, old) {
      if (newVal.length) {
        this.todos = this.origin_todos.filter(todo =>
          todo.title.match(new RegExp(`^${newVal}`, "i"))
        );
        this.resetPosition();
      } else {
        this.todos = this.origin_todos.slice(0, this.origin_todos.length);
        this.resetPosition();
      }
    }
  },
  mounted() {
    fetch("http://localhost:8000/todo/")
      .then(response => response.json())
      .then(data => {
        console.log(data);
        this.origin_todos = data.reverse();
        this.todos = this.origin_todos.slice(0, this.origin_todos.length);
        this.end = data.length < this.perPage ? data.length : this.perPage;
        this.totalRows = data.length;
      });
  }
};
</script>

<style>
.true .card-header {
  background-color: #00ff1a42;
}
</style>
