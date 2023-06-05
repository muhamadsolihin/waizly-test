<template>
  <div class="container">
    <div class="d-flex flex-row">
      <h3 style="font-weight: 700">Todo List</h3>
      <div class="d-flex ms-auto">Current Time: {{ currentTime }}</div>
    </div>
    <div class="card mt-2">
      <div class="card-body">
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
                    <i class="bi bi-pencil-square"></i>
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
                    <i class="bi bi-trash-fill"></i>
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
  padding: 3%;
  max-width: 100%;
}

.completed {
  text-decoration: line-through;
}

.btn {
  display: flex;
  align-items: center;
}

.btn i {
  margin-right: 5px;
}

table {
  margin-top: 20px;
}

input[type="text"] {
  border-radius: 0;
}

input[type="submit"] {
  border-radius: 0;
}
</style>
