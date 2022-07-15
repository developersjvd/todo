<template>
  <div>
    <div class="container w-full max-w-2xl" id="app">
      <div class="text-3xl text-center text-green-600 font-bold mb-3 uppercase">
        Lista de tareas
      </div>
      <div>
        <form
          action="#"
          @submit.prevent="addTodo()"
          method="POST"
          id="add_todo_form"
          class="flex justify-center"
        >
          <input
            type="text"
            name="todo"
            v-model="newTodo"
            placeholder="Agregar nueva tarea"
            class="text-xl text-green-800 placeholder-green-400 py-2 px-5 bg-green-100 rounded-l-full focus:outline-none focus-visible:outline-none"
          />
          <button
            type="submit"
            class="text-xl text-green-100 placeholder-green-400 py-2 pr-5 pl-4 bg-green-500 rounded-r-full"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              class="h-6 w-6"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
              stroke-width="2"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M12 4v16m8-8H4"
              />
            </svg>
          </button>
        </form>
      </div>
      <div class="bg-gray-100 mt-5 p-5 rounded-xl shadow-lg text-gray-700">
        <h1 class="font-bold text-xl italic block mb-0 leading-none">Tareas</h1>
        <small id="todo_stats" class="block mb-5 mt-0 text-xs text-gray-500"
          >{{ remaining }} Pendientes, {{ completed }} Completados.</small
        >
        <div class="max-h-80 overflow-y-auto">
          <table id="todo_table" class="table w-full">
            <thead>
              <tr>
                <th
                  class="text-center px-1 py-2 bg-green-500 text-green-100 rounded-tl-xl"
                >
                  #
                </th>
                <th class="text-left px-1 py-2 bg-green-500 text-green-100">
                  Descripción
                </th>
                <th class="px-1 py-2 bg-green-500 text-green-100 rounded-tr-xl">
                  Acción
                </th>
              </tr>
            </thead>
            <tbody>
              <tr
                v-if="todos.length <= 0"
                class="odd:bg-green-100 even:bg-green-50"
              >
                <td class="text-center px-1 py-2 text-green-800" colspan="3">
                  Sin tareas encontradas. Agrega algunas para comenzar.
                </td>
              </tr>
              <tr
                v-for="(todo, key) in todos"
                :key="key"
                :class="`${
                  !todo.completed
                    ? 'odd:bg-green-100 even:bg-green-50 transition duration-300'
                    : 'bg-green-100 line-through'
                } `"
              >
                <td class="text-center px-1 py-2 text-green-800">
                  {{ key + 1 }}
                </td>
                <td class="px-1 py-2 text-green-800">
                  <div v-if="!todo.edit" @dblclick="editTodo(todo)">
                    {{ todo.title }}
                  </div>
                  <input
                    class="text-green-800 placeholder-green-400 py-2 px-5 bg-green-100 focus:outline-none focus-visible:outline-none"
                    v-else
                    type="text"
                    v-model="todo.title"
                    @blur="doneEdit(todo)"
                    @keyup.enter="doneEdit(todo)"
                    @keyup.esc="cancelEdit(todo)"
                  />
                </td>
                <td
                  class="text-center px-1 py-2 text-green-800 flex gap-3 justify-start"
                >
                  <button
                    v-if="!todo.completed"
                    @click.prevent="markTodoComplete(key)"
                    class="text-green-600"
                  >
                    <svg
                      xmlns="http://www.w3.org/2000/svg"
                      class="h-6 w-6"
                      fill="none"
                      viewBox="0 0 24 24"
                      stroke="currentColor"
                      stroke-width="2"
                    >
                      <path
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        d="M5 13l4 4L19 7"
                      />
                    </svg>
                  </button>
                  <button
                    @click.prevent="markTodoIncomplete(key)"
                    v-if="todo.completed"
                    class="text-green-600"
                  >
                    <svg
                      xmlns="http://www.w3.org/2000/svg"
                      class="h-6 w-6"
                      fill="none"
                      viewBox="0 0 24 24"
                      stroke="currentColor"
                      stroke-width="2"
                    >
                      <path
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        d="M6 18L18 6M6 6l12 12"
                      />
                    </svg>
                  </button>
                  <button
                    v-if="todo.completed"
                    @click.prevent="removeTodo(key)"
                    class="text-green-600"
                  >
                    <svg
                      xmlns="http://www.w3.org/2000/svg"
                      class="h-6 w-6"
                      fill="none"
                      viewBox="0 0 24 24"
                      stroke="currentColor"
                      stroke-width="2"
                    >
                      <path
                        stroke-linecap="round"
                        stroke-linejoin="round"
                        d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"
                      />
                    </svg>
                  </button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ToDoList",
  data() {
    return {
      newTodo: "",
      beforeEditCache: "",
      todos: [
        {
          id: 0,
          title: "Tarea de ejemplo 1",
          completed: false,
          edit: false,
        },
        {
          id: 1,
          title: "Tarea de ejemplo 2",
          completed: false,
          edit: false,
        },
      ],
    };
  },
  methods: {
    addTodo() {
      if (this.newTodo == "") {
        return;
      }
      this.todos.push({
        title: this.newTodo,
        completed: false,
        edit: false,
      });
      this.newTodo = "";
    },
    editTodo(todo) {
      this.beforeEditCache = todo.title;
      todo.edit = true;
    },
    doneEdit(todo) {
      if (todo.title.trim() == "") {
        todo.title = this.beforeEditCache;
      }
      todo.edit = false;
    },
    cancelEdit(todo) {
      todo.title = this.beforeEditCache;
      todo.edit = false;
    },
    markTodoComplete(key) {
      this.todos[key].completed = true;
    },
    markTodoIncomplete(key) {
      this.todos[key].completed = false;
    },
    removeTodo(key) {
      this.todos.splice(key, 1);
    },
    handleEdit(key) {
      const result = this.todos.find((todo) => todo.id === key);
      this.newTodo = result.title;
    },
  },
  computed: {
    remaining() {
      return this.todos.filter((todo) => !todo.completed).length;
    },
    completed() {
      return this.todos.filter((todo) => todo.completed).length;
    },
  },
};
</script>

<style></style>
