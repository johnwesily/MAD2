<!-- src/views/Login.vue -->
<template>
    <div id="app">
      <h1>Vue.js & Flask User Authentication</h1>
      <div v-if="!isAuthenticated">
        <h2>Register</h2>
        <form @submit.prevent="register">
          <input v-model="registerData.username" placeholder="Username" required>
          <input type="password" v-model="registerData.password" placeholder="Password" required>
          <button type="submit">Register</button>
        </form>
  
        <h2>Login</h2>
        <form @submit.prevent="login">
          <input v-model="loginData.username" placeholder="Username" required>
          <input type="password" v-model="loginData.password" placeholder="Password" required>
          <button type="submit">Login</button>
        </form>
      </div>
  
      <div v-else>
        <h2>Protected Content</h2>
        <p>{{ protectedMessage }}</p>
        <button @click="logout">Logout</button>
      </div>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    name: 'LoginPage',
    data() {
      return {
        registerData: {
          username: '',
          password: ''
        },
        loginData: {
          username: '',
          password: ''
        },
        protectedMessage: '',
        isAuthenticated: false
      };
    },
    methods: {
      async register() {
        try {
          await axios.post('http://127.0.0.1:5000/register', this.registerData);
          alert('User registered successfully');
        } catch (error) {
          console.error(error);
          alert('Registration failed');
        }
      },
      async login() {
        try {
          const response = await axios.post('http://127.0.0.1:5000/login', this.loginData);
          localStorage.setItem('token', response.data.access_token);
          this.isAuthenticated = true;
          this.getProtectedMessage();
        } catch (error) {
          console.error(error);
          alert('Login failed');
        }
      },
      async getProtectedMessage() {
        try {
          const response = await axios.get('http://127.0.0.1:5000/protected', {
            headers: {
              Authorization: `Bearer ${localStorage.getItem('token')}`
            }
          });
          this.protectedMessage = response.data.logged_in_as.username;
        } catch (error) {
          console.error(error);
          alert('Failed to fetch protected message');
        }
      },
      logout() {
        localStorage.removeItem('token');
        this.isAuthenticated = false;
        this.protectedMessage = '';
      }
    }
  };
  </script>
  
  <style>
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    margin-top: 60px;
  }
  </style>
  