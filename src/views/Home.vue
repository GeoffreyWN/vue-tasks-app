<template>
    <AddTask v-if="showAddTask" @add-task="addTask" />

  <Tasks
    @toggle-reminder="toggleReminder"
    @delete-task="deleteTask"
    :tasks="tasks"
  />
</template>

<script>
import Tasks from '../components/Tasks.vue'
import AddTask from '../components/AddTask.vue'

export default {
  name: 'Home',
  components: { Tasks, AddTask },
  props: { 
      showAddTask: Boolean
  },

  data() {
    return {
      tasks: [],
    }
  },

  methods: {
    async addTask(newTask) {
      const config = {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(newTask),
      }
      const res = await fetch('api/tasks', config)

      const data = await res.json()

      this.tasks.push(data)
    },

    async deleteTask(id) {
      if (confirm('Kindly confirm delete!')) {
        console.log('delete task', id)

        const config = {
          method: 'DELETE',
        }

        const res = await fetch(`/api/tasks/${id}`, config)

        res.status === 200
          ? (this.tasks = this.tasks.filter((task) => task.id !== id))
          : alert('An error occurred while deleting task')
      }
    },

    async toggleReminder(id) {
      const taskToToggle = await this.fetchTask(id)
      const updatedTask = { ...taskToToggle, reminder: !taskToToggle.reminder }

      const config = {
        method: 'PUT',
        headers: {
          'Content-type': 'application/json',
        },
        body: JSON.stringify(updatedTask),
      }

      const res = await fetch(`api/tasks/${id}`, config)

      const data = await res.json()

      this.tasks = this.tasks.map((task) =>
        task.id === id ? { ...task, reminder: data.reminder } : task
      )
    },
    async fetchTasks() {
      const res = await fetch('api/tasks')
      const data = await res.json()

      return data
    },

    async fetchTask(id) {
      const res = await fetch(`api/tasks/${id}`)
      const data = await res.json()

      return data
    },
  },

  async created() {
    //register side effects
    this.tasks = await this.fetchTasks()
  },
}
</script>
