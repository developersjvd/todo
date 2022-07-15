<template>
  <div id="app" class="bg-green-200 flex justify-center items-center h-screen">
    <ToDoList/>
  </div>
</template>

<script>
import ToDoList from './components/ToDoList.vue'

export default {
  name: 'App',
  components: {
    ToDoList
  }
}
</script>