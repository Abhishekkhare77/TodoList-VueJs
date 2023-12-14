<template>
  <AuthModal :show="showAuthModal" @closeModal="showAuthModal = false" @login-success="refreshPage"/>
  <div class="fixed top-1 right-1">
    <button class="px-4 py-2 bg-red-500 rounded text-white hover:bg-red-600" @click="logout()">Logout</button>
  </div>
  <div class="flex flex-col sm:flex-row justify-around items-center">
    <AddTask @task-added="refreshTaskList" class="sm:w-1/2" />
    <TaskList ref="taskList" class="sm:w-1/2" />
  </div>
</template>

<script>
import AuthModal from "@/components/AuthModal.vue";
import AddTask from "./components/AddTask.vue";
import TaskList from "./components/TaskList.vue";

export default {
  name: 'App',
  components: {
    AuthModal,
    AddTask,
    TaskList
  },
  data() {
    return {
      showAuthModal: localStorage.getItem("userId") ? false : true
    }
  },
  methods: {
    refreshTaskList() {
      this.$refs.taskList.fetchTasks(); 
    },
    refreshPage(){
      window.location.reload();
    },
    logout(){
      localStorage.clear(),
      this.showAuthModal =true;
    }
  }
}
</script>

<style>
@tailwind base;
@tailwind components;
@tailwind utilities;
</style>
