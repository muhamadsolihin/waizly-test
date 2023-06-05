<template>
  <div class="container">
    <div class="d-flex flex-row">
      <h3 style="font-weight: 700">Todo List</h3>
      <div class="d-flex ms-auto">Current Time: {{ currentTime }}</div>
    </div>
    <div class="card mt-2">
      <div class="card-body">
        <!-- Todo input form -->

        <div class="row">
          <div class="col-md-6">
            <form @submit.prevent="addTodo">
              <div class="input-group mb-3">
                <input
                  type="text"
                  class="form-control"
                  v-model="newTodo"
                  placeholder="Enter a new todo item"
                  required
                />
                <button class="btn btn-primary" type="submit">Add</button>
              </div>
            </form>
          </div>
          <!-- Search input -->
          <div class="col-md-6">
            <div class="input-group mb-3">
              <input
                type="text"
                class="form-control"
                v-model="searchQuery"
                placeholder="Search todos"
              />
            </div>
          </div>
        </div>

        <table class="table table-striped table-bordered">
          <thead>
            <tr>
              <th scope="col-md-4">No</th>
              <th scope="col-md-4">Todo</th>
              <th scope="col-md-4">Actions</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(todo, index) in filteredTodos" :key="index">
              <th scope="row">{{ index + 1 }}</th>
              <td>
                <div
                  v-if="!todo.editing"
                  :class="{ completed: todo.completed }"
                >
                  {{ todo.text }}
                </div>
                <div v-else>
                  <input
                    type="text"
                    class="form-control"
                    v-model="todo.updatedText"
                    required
                  />
                </div>
              </td>
              <td class="col-md-2">
                <div class="d-flex justify-content-between">
                  <button
                    class="btn btn-sm btn-primary"
                    v-if="!todo.editing"
                    @click="editTodo(index)"
                  >
                    <svg
                      xmlns="http://www.w3.org/2000/svg"
                      width="16"
                      height="16"
                      fill="currentColor"
                      class="bi bi-pencil-square"
                      viewBox="0 0 16 16"
                    >
                      <path
                        d="M15.502 1.94a.5.5 0 0 1 0 .706L14.459 3.69l-2-2L13.502.646a.5.5 0 0 1 .707 0l1.293 1.293zm-1.75 2.456-2-2L4.939 9.21a.5.5 0 0 0-.121.196l-.805 2.414a.25.25 0 0 0 .316.316l2.414-.805a.5.5 0 0 0 .196-.12l6.813-6.814z"
                      />
                      <path
                        fill-rule="evenodd"
                        d="M1 13.5A1.5 1.5 0 0 0 2.5 15h11a1.5 1.5 0 0 0 1.5-1.5v-6a.5.5 0 0 0-1 0v6a.5.5 0 0 1-.5.5h-11a.5.5 0 0 1-.5-.5v-11a.5.5 0 0 1 .5-.5H9a.5.5 0 0 0 0-1H2.5A1.5 1.5 0 0 0 1 2.5v11z"
                      />
                    </svg>
                  </button>
                  <button
                    class="btn btn-sm btn-success"
                    v-else
                    @click="saveTodo(index)"
                  >
                    Save
                  </button>
                  <button
                    class="btn btn-sm btn-danger"
                    @click="deleteTodo(index)"
                  >
                    <svg
                      xmlns="http://www.w3.org/2000/svg"
                      width="16"
                      height="16"
                      fill="currentColor"
                      class="bi bi-trash-fill"
                      viewBox="0 0 16 16"
                    >
                      <path
                        d="M2.5 1a1 1 0 0 0-1 1v1a1 1 0 0 0 1 1H3v9a2 2 0 0 0 2 2h6a2 2 0 0 0 2-2V4h.5a1 1 0 0 0 1-1V2a1 1 0 0 0-1-1H10a1 1 0 0 0-1-1H7a1 1 0 0 0-1 1H2.5zm3 4a.5.5 0 0 1 .5.5v7a.5.5 0 0 1-1 0v-7a.5.5 0 0 1 .5-.5zM8 5a.5.5 0 0 1 .5.5v7a.5.5 0 0 1-1 0v-7A.5.5 0 0 1 8 5zm3 .5v7a.5.5 0 0 1-1 0v-7a.5.5 0 0 1 1 0z"
                      />
                    </svg>
                  </button>
                  <button
                    class="btn btn-sm btn-success"
                    @click="toggleComplete(index)"
                  >
                    {{ todo.completed ? "Undo" : "Complete" }}
                  </button>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";

const currentTime = ref("");
const todos = ref([]);
const newTodo = ref("");
const searchQuery = ref("");

function updateTime() {
  const date = new Date();
  currentTime.value = date.toLocaleTimeString();
}

const addTodo = () => {
  if (newTodo.value.trim() !== "") {
    todos.value.push({
      text: newTodo.value,
      editing: false,
      completed: false,
      updatedText: "",
    });
    newTodo.value = "";
  }
};

const editTodo = (index) => {
  todos.value[index].editing = true;
  todos.value[index].updatedText = todos.value[index].text;
};

const saveTodo = (index) => {
  const updatedText = todos.value[index].updatedText.trim();
  if (updatedText !== "") {
    todos.value[index].text = updatedText;
    todos.value[index].editing = false;
  }
};

const deleteTodo = (index) => {
  todos.value.splice(index, 1);
};

const toggleComplete = (index) => {
  todos.value[index].completed = !todos.value[index].completed;
};

const filteredTodos = computed(() => {
  return todos.value.filter((todo) => {
    return todo.text.toLowerCase().includes(searchQuery.value.toLowerCase());
  });
});
onMounted(() => {
  updateTime();
  setInterval(updateTime, 1000);
});
</script>

<style lang="scss" scoped>
.container {
  padding: 5%;
  max-width: 100%;
}

.completed {
  text-decoration: line-through;
}
</style>
