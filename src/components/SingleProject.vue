<template>
  <div class="project" :class="{ complete: project.complete }">
    <div class="actions">
      <h3 @click="toggleVisibility">{{ project.title }}</h3>
      <div class="icons">
        <router-link :to="{ name: 'EditProject', params: { id: project.id }}">
          <span class="material-icons">edit</span>
        </router-link>
        <span @click="deleteProject" class="material-icons">delete</span>
        <span @click="toggleComplete" class="material-icons tick">done</span>
      </div>
    </div>
    <div class="details" v-show="visible">
      <p>{{ project.details }}</p>
    </div>
  </div>
</template>

<script>
import { projectFirestore } from '../firebase/config'
export default {
  name: 'SingleProject',
  props: ['project'],
  setup(props) {
    const visible = false

    const toggleVisibility = () => {
      visible.value = !visible.value
    }

    const deleteProject = () => {
      projectFirestore.collection('projects').delete(props.project)
    }

    const toggleComplete = () => {
      fetch(this.uri, { 
        method: 'PATCH', 
        headers: { 'content-type': 'application/json' },
        body: JSON.stringify({ complete: !this.project.complete })
      }).then(() => {
        this.$emit('complete', this.project.id)
      }).catch(err => console.log(err.message))
    }

    return { visible, toggleVisibility, toggleComplete, deleteProject }
  }
}
</script>

<style scoped>
.project {
  margin: 20px auto;
  background: white;
  padding: 10px 20px;
  border-radius: 4px;
  box-shadow: 1px 2px 3px rgba(0,0,0,0.05);
  border-left: 4px solid #e90074;
}
h3 {
  cursor: pointer;
}
.actions {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.material-icons {
  font-size: 24px;
  margin-left: 10px;
  color: #bbb;
  cursor: pointer;
}
.material-icons:hover {
  color: #777;
}
.project.complete {
  border-left-color: #00ce89;
}
.project.complete .tick {
  color: #00ce89;
}
@media only screen and (max-width: 440px) {
  .actions {
    display: flex;
    flex-direction: column-reverse;
    justify-content: center;
    text-align: center;
  }
  .material-icons {
    margin-top: 0.5rem;
  }
}
</style>