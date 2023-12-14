<template>
    <div class="flex justify-center items-center h-1/2 sm:h-screen bg-gray-100">
        <form @submit.prevent="addTask" class="bg-white shadow-md rounded px-8 pt-6 pb-8 mb-4 w-screen sm:w-auto">
            <div class="mb-4">
                <label class="block text-gray-700 text-sm font-bold mb-2" for="title">
                    Title
                </label>
                <input
                    class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                    id="title" type="text" placeholder="Task Title" v-model="task.title">
            </div>
            <div class="mb-6">
                <label class="block text-gray-700 text-sm font-bold mb-2" for="body">
                    Description
                </label>
                <textarea
                    class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 mb-3 leading-tight focus:outline-none focus:shadow-outline"
                    id="body" placeholder="Task Description" v-model="task.body"></textarea>
            </div>
            <div class="flex items-center justify-between">
                <button
                    class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline"
                    type="submit">
                    Add Task
                </button>
            </div>
        </form>
    </div>
</template>
  
<script>

import { toast } from 'vue3-toastify';
import 'vue3-toastify/dist/index.css';

export default {
    name: 'AddTask',
    data() {
        return {
            task: {
                title: '',
                body: '',
                id: localStorage.getItem("userId") 
            }
        }
    },
    methods: {
        async addTask() {
            try {
                const response = await fetch('https://backend-todolist-pxcq.onrender.com/api/v2/addTask', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify(this.task)
                });
                console.log(response);
                toast.success("Task Added Successfully!")
                this.$emit('task-added'); 
            } catch (error) {
                console.error(error);
                toast.error("Please try again .")
            }
        }
    }
}
</script>