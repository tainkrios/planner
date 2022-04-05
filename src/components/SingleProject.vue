<template>
  <div class="project" :class="{ complete: project.complete }">
    <div class="actions">
      <h3 @click="detailsToggle">{{ project.title }}</h3>
      <div class="icons">
        <router-link
          :to="{
            name: 'EditProject',
            params: {
              id: project.id
            }
          }"
        >
          <span class="material-icons">edit</span>
        </router-link>
        <span @click="deleteProject" class="material-icons">delete</span>
        <span @click="completeProject" class="material-icons tick">done</span>
      </div>
    </div>
    <div class="details" v-if="state.showDetails">
      <p>{{ project.details }}</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { reactive } from "vue";

interface State {
  showDetails: boolean;
  uri: string;
}

interface Props {
  project: {
    id: number | undefined
    title: string
    details: string
    complete: boolean | undefined
  }
}

const props = defineProps<Props>()

const state = reactive<State>({
  showDetails: false,
  uri: "http://localhost:3000/projects/" + props.project.id,
})

const emit = defineEmits<{
  (e: "delete", value: number): void
  (e: "update", value: number): void
}>()

function detailsToggle() {
  state.showDetails = !state.showDetails;
}

function deleteProject() {
  fetch(state.uri, { method: "DELETE" })
    .then(() => emit("delete", props.project.id))
    .catch((err) => console.log(err.message))
}

function completeProject() {
  fetch(state.uri, {
    method: "PATCH",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ complete: !props.project.complete }),
  })
    .then(() => emit("update", props.project.id))
    .catch((err) => console.log(err));
}
</script>

<style scoped lang="scss">
.project {
  margin: 20px auto;
  background: white;
  padding: 10px 20px;
  border-radius: 5px;
  box-shadow: 1px 2px 3px rgba(0, 0, 0, 0.05);
  border-left: 5px solid #e90074;
}

.project.complete {
  border-left: 5px solid #00ce89;
}

.project.complete .tick {
  color: #00ce89;
}

h3 {
  cursor: pointer;
}

.actions {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.icons {
  display: flex;
}

.material-icons {
  font-size: 24px;
  margin-left: 10px;
  color: #bbb;
  cursor: pointer;

  &:hover {
    color: #777;
  }
}
</style>