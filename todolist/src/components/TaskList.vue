<template>
    <div class="container mx-auto p-4">
        <div v-if="tasks.length" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
            <div v-for="task in tasks" :key="task._id" class="bg-white p-4 rounded-lg shadow">
                <h2 class="text-xl font-bold mb-2" :class="{'line-through': task.done}">{{ task.title }}</h2>
                <p>{{ task.body }}</p>
                <button @click="editTask(task)"
                    class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-1 px-2 rounded m-1">Edit</button>
                <button @click="deleteTask(task._id)"
                    class="bg-red-500 hover:bg-red-700 text-white font-bold py-1 px-2 rounded m-1">Delete</button>
                <button @click="markTaskAsDone(task._id)"
                    class="bg-green-500 hover:bg-green-700 text-white font-bold py-1 px-2 rounded m-1">
                    Done
                </button>
            </div>
        </div>
        <div v-else class="text-center">
            <p>No tasks found.</p>
        </div>
    </div>
    <div v-if="showEditModal" class="fixed inset-0 bg-gray-500 bg-opacity-75 flex justify-center items-center">
        <div class="bg-white p-4 rounded-lg shadow-lg">
            <h3 class="text-lg font-bold">Edit Task</h3>
            <input v-model="editTaskData.title" class="w-full p-2 my-2 border rounded" placeholder="Title">
            <textarea v-model="editTaskData.body" class="w-full p-2 my-2 border rounded" placeholder="Body"></textarea>
            <button @click="updateTask"
                class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-1 px-2 rounded m-1">Save</button>
            <button @click="showEditModal = false"
                class="bg-gray-500 hover:bg-gray-700 text-white font-bold py-1 px-2 rounded m-1">Cancel</button>
        </div>
    </div>
</template>
  
  
<script>
import { toast } from 'vue3-toastify';
import 'vue3-toastify/dist/index.css';
export default {
    name: 'TasksList',
    data() {
        return {
            tasks: [],
            userId: localStorage.getItem("userId"),
            showEditModal: false,
            editTaskData: {
                _id: null,
                title: '',
                body: ''
            }
        }
    },
    created() {
        this.fetchTasks();
    },
    methods: {
        async fetchTasks() {
            try {
                const response = await fetch(`https://backend-todolist-pxcq.onrender.com/api/v2/getTasks/${this.userId}`);
                if (!response.ok) {
                    throw new Error(`HTTP error! Status: ${response.status}`);
                }
                const data = await response.json();
                this.tasks = data.list;
            } catch (error) {
                console.error("Error fetching tasks:", error);
            }
        },

        editTask(task) {
            this.editTaskData = { ...task };
            this.showEditModal = true;
        },

        async updateTask() {
            try {
                const response = await fetch(`https://backend-todolist-pxcq.onrender.com/api/v2/updateTask/${this.editTaskData._id}`, {
                    method: 'PUT',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({
                        title: this.editTaskData.title,
                        body: this.editTaskData.body
                    })
                });
                if (!response.ok) {
                    throw new Error(`HTTP error! Status: ${response.status}`);
                }
                // Find the index of the task being updated
                const index = this.tasks.findIndex(task => task._id === this.editTaskData._id);

                // Update the task in the tasks array
                if (index !== -1) {
                    this.tasks[index] = { ...this.tasks[index], ...this.editTaskData };
                }

                this.showEditModal = false;
                toast.success("Task updated successfully!");
            } catch (error) {
                console.error("Error updating task:", error);
                toast.error("Error updating task. Please try again.");
            }
        },


        async deleteTask(id) {
            try {
                const response = await fetch(`https://backend-todolist-pxcq.onrender.com/api/v2/deleteTask/${id}`, {
                    method: 'DELETE'
                });
                if (!response.ok) {
                    throw new Error(`HTTP error! Status: ${response.status}`);
                }
                // Remove the task from the list
                this.tasks = this.tasks.filter(task => task._id !== id);
                toast.success("Task deleted Successfully !")
            } catch (error) {
                console.error("Error deleting task:", error);
                toast.error("Some error occured!")
            }
        },

        async markTaskAsDone(id) {
            try {
                const response = await fetch(`https://backend-todolist-pxcq.onrender.com/api/v2/markTaskAsDone/${id}`, {
                    method: 'PUT'
                });
                if (!response.ok) {
                    throw new Error(`HTTP error! Status: ${response.status}`);
                }
                // Update the task in the tasks array
                const index = this.tasks.findIndex(task => task._id === id);
                if (index !== -1) {
                    this.tasks[index].done = true;
                }
                toast.success("Task marked as done!");
            } catch (error) {
                console.error("Error marking task as done:", error);
                toast.error("Error marking task as done. Please try again.");
            }
        },
    }
}
</script>
  