<template>
  <form @submit.prevent="handleSubmit">
    <label>Title:</label>
    <input type="text" v-model="state.title" required>
    <label>Details:</label>
    <textarea v-model="state.details" required></textarea>
    <button>Add Project</button>
  </form>
</template>

<script setup lang="ts">
import { reactive } from "vue";
import { useRouter } from "vue-router";

interface State {
  title: string
  details: string
}

const state = reactive<State>(
  {
    title: '',
    details: ''
  }
)

const router = useRouter()

function handleSubmit() {
  let project = {
    title: state.title,
    details: state.details,
    complete: false
  }

  fetch('http://localhost:3000/projects', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(project)
  }).then(() => {
    router.push({name:'Home'})
  })
}

</script>

<style scoped>
form {
    background: white;
    padding: 20px;
    border-radius: 10px;
  }
  label {
    display: block;
    color: #bbb;
    text-transform: uppercase;
    font-size: 14px;
    font-weight: bold;
    letter-spacing: 1px;
    margin: 20px 0 10px 0
  }
  input {
    padding: 10px;
    border: 0;
    border-bottom: 1px solid #ddd;
    width: 100%;
    box-sizing: border-box;
  }
  textarea {
    border: 1px solid #ddd;
    padding: 10px;
    width: 100%;
    box-sizing: border-box;
    height: 100px;
  }
  form button {
    display: block;
    margin: 20px auto 0;
    background: #00ce89;
    color: white;
    padding: 10px;
    border: 0;
    border-radius: 6px;
    font-size: 16px;
    cursor: pointer;
  }
</style>
