<template>
    <div v-if="show" class="fixed inset-0 bg-gray-600 bg-opacity-50 overflow-y-auto h-full w-full">
        <div class="relative top-20 mx-auto p-5 border w-96 shadow-lg rounded-md bg-white">
            <div class="mt-3 text-center">
                <div v-if="isLogin">
                    <h3 class="text-lg leading-6 font-medium text-gray-900">Login</h3>
                    <form @submit.prevent="handleLogin">
                        <input type="email" v-model="email" placeholder="Email" class="mt-2 p-2 w-full border rounded">
                        <input type="password" v-model="password" placeholder="Password"
                            class="mt-2 p-2 w-full border rounded">
                        <button type="submit"
                            class="mt-4 px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-700">Login</button>
                    </form>
                </div>
                <div v-else>
                    <h3 class="text-lg leading-6 font-medium text-gray-900">Sign Up</h3>
                    <form @submit.prevent="handleSignup">
                        <input type="email" v-model="email" placeholder="Email" class="mt-2 p-2 w-full border rounded">
                        <input type="text" v-model="username" placeholder="Username" class="mt-2 p-2 w-full border rounded">
                        <input type="password" v-model="password" placeholder="Password"
                            class="mt-2 p-2 w-full border rounded">
                        <button type="submit" class="mt-4 px-4 py-2 bg-green-500 text-white rounded hover:bg-green-700">Sign
                            Up</button>
                    </form>
                </div>
                <div class="mt-2">
                    <button @click="toggleForm" class="text-sm text-blue-500 hover:text-blue-800">{{ isLogin ? `Need an
                        account ? Sign up` : `Already have an account ? Login` }}</button>
                </div>
            </div>
        </div>
    </div>
</template>
  
<script>
import { toast } from 'vue3-toastify';
import 'vue3-toastify/dist/index.css';

export default {
    props: ['show'],
    data() {
        return {
            isLogin: true,
            email: '',
            username: '',
            password: ''
        };
    },
    methods: {
        toggleForm() {
            this.isLogin = !this.isLogin;
        },
        async handleLogin() {
            try {
                const response = await fetch('https://backend-todolist-pxcq.onrender.com/api/v1/signin', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({
                        email: this.email,
                        password: this.password,
                    }),
                });
                const data = await response.json();
                if (response.ok) {
                    console.log('Login Successful:', data);
                    localStorage.setItem('userId',data.user._id);
                    toast.success('Login successful!');
                    this.$emit('closeModal');
                    this.$emit('login-success');
                } else {
                    console.error('Login Failed:', data.message);
                    toast.error("Login Failed!")
                }
            } catch (error) {
                console.error('Error:', error);
            }
        },

        async handleSignup() {
            try {
                const response = await fetch('https://backend-todolist-pxcq.onrender.com/api/v1/register', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json',
                    },
                    body: JSON.stringify({
                        email: this.email,
                        username: this.username,
                        password: this.password,
                    }),
                });
                const data = await response.json();
                if (response.ok) {
                    console.log('Signup Successful:', data);
                    toast.success('Signup successful!');
                    this.toggleForm()
                } else {
                    console.error('Signup Failed:', data.message);
                }
            } catch (error) {
                console.error('Error:', error);
            }
        },
    }
};
</script>
  