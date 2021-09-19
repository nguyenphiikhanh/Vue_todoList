<template>
  <div class="row todo-content">
    <div class="col-md-6">
      <div class="todo-list not-done">
        <h1>TODO List App</h1>
        <div class="input-group mb-3">
          <input
            type="text"
            class="form-control"
            v-model="textContent"
            placeholder="Enter content"
          />
          <div class="input-group-append">
            <button
              @click="addTask()"
              class="btn btn-outline-secondary"
              type="button"
              id="button-addon2"
            >
              Add
            </button>
          </div>
        </div>
        <hr />
        <ul class="list-unstyled" v-for="todo in todos" :key="todo.id">
          <li class="ui-state-default li-items mt-1">
            <div class="input-group">
              <div class="input-group-prepend">
                <div class="input-group-text">
                  <input
                    type="checkbox"
                    v-model="todo.checked"
                    :checked="todo.checked"
                    aria-label="Radio button for following text input"
                  />
                </div>
              </div>
              <input
                type="text"
                :class="{ 'done-task': todo.completed }"
                class="form-control"
                v-model="todo.content"
              />
              <div class="input-group-append remove-icon">
                <span class="input-group-text" @click="delTask(todo.id)"
                  >&#10060;</span
                >
              </div>
            </div>
          </li>
        </ul>
        <hr />
        <div class="todo-footer row">
          <div class="col-md-6">
            <div class="form-check form-check-inline">
              &#9989;
              <label
                @click="checkAll(1)"
                class="form-check-label"
                for="inlineRadio1"
                >Check all</label
              >
            </div>
            <div class="form-check form-check-inline">
              &#10062;
              <label
                @click="checkAll(0)"
                class="form-check-label"
                for="inlineRadio2"
                >UnCheck all</label
              >
            </div>
          </div>
          <div class="col-md-6 save-all">
            <div class="form-check form-check-inline save-all">
              <button
                @click="doneAll()"
                type="button"
                class="btn btn-success btn-sm"
              >
                DONE ALL &#10004;
              </button>
            </div>
            <div class="form-check form-check-inline save-all">
              <button
                @click="delAll()"
                type="button"
                class="btn btn-dark btn-sm"
              >
                DEL ALL &#10006;
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

name: "Todos";
export default {
  data: function () {
    return {
      todos: [],
      textContent: "",
    };
  },
  created() {
    axios.get("http://localhost:8000/api/tasks").then((Response) => {
      this.todos = Response.data.data;
    });
  },
  methods: {
    addTask: function () {
      if (this.textContent.trim().length === 0) {
        return;
      }
      let params = {
        content: this.textContent,
        checked: false,
        completed: false,
      };

      axios.post("http://localhost:8000/api/tasks", params).then((Response) => {
        this.todos = Response.data.data;
        this.textContent = "";
      });
    },
    delTask: function (index) {
      if (confirm("ban co chac chan xoa task nay khong?")) {
        axios
          .get("http://localhost:8000/api/remove", { params: { id: index } })
          .then((Response) => {
            this.todos = Response.data.data;
          });
      }
    },
    checkAll: function (flag) {
      this.todos.forEach((todo) => {
        todo.checked = flag;
      });
    },
    doneAll: function () {
      if (confirm("Hoan thanh cac task nay?")) {
        let params = this.todos;
        axios.post('http://localhost:8000/api/doneTask',{params}).
        then(Response=>{
          this.todos = Response.data.data;
        });
      }
    },
    delAll: function () {
      if (confirm("Ban co chac chan xoa cac task nay?")) {
        let params = this.todos;
        axios
          .post("http://localhost:8000/api/multiRemove",{params})
          .then((Response) => {
            this.todos = Response.data.data;
          });
      }
    },
  },
};
</script>

<style scoped>
.todo-content {
  display: flex;
  justify-content: center;
}

.todo-list h1 {
  margin-top: 20px;
  padding-bottom: 20px;
  text-align: center;
  font-weight: bold;
}

.todo-footer {
  background-color: #d2e8ca;
  padding: 10px 20px 15px;
}

#done-items li {
  padding: 10px 0;
  border-bottom: 1px solid #ddd;
  text-decoration: line-through;
}

.done-task {
  text-decoration: line-through;
  color: orange;
}

.form-check-inline {
  cursor: pointer;
}

.remove-icon span {
  cursor: pointer;
}

.save-all {
  float: right;
}
</style>