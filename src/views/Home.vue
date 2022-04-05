<template>
  <div class="home">
    <FilterNav @filterChange="changeToggle" :current="state.current"/>
    <div v-if="state.projects.length">
      <div v-for="project in filteredProjects" :key="project.id">
        <SingleProject
          :project = "project"
          @delete = "handleDelete"
          @update = "handleUpdate"
        />
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { computed, onMounted, reactive } from "@vue/runtime-core"
import SingleProject from "@/components/SingleProject.vue"
import FilterNav from "@/components/FilterNav.vue"

interface State {
  projects: {
    id: number | undefined
    title: string;
    details: string;
    complete: boolean | undefined
  }[],
  current: string
}

const state = reactive<State>({
  projects: [
    {
      id: 0,
      title: "",
      details: "",
      complete: undefined
    }
  ],
  current: 'all'
})

onMounted(() => {
  fetch("http://localhost:3000/projects")
    .then((res) => res.json())
    .then((data) => (state.projects = data))
    .catch((err) => console.log(err.message))
})

function handleDelete(id: number) {
  state.projects = state.projects.filter((project) => {
    return project.id !== id
  })
}

function handleUpdate(id: number) {
  let p = state.projects.find((project) => {
    return project.id === id
  })
  p.complete = !p.complete;
}

function changeToggle(by:string) {
  return state.current = by
}

const filteredProjects = computed(() => {
  if (state.current === 'completed') {
    return state.projects.filter( project => project.complete )
  }
  if (state.current === 'ongoing') {
    return state.projects.filter( project => !project.complete )
  } 
  return state.projects
  
})
</script>
