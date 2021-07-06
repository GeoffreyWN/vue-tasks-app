<template>
  <div class="container">
    <Header
      @toggle-add-task="toggleAddTask"
      :showAddTask="showAddTask"
      title="TrackMyTasks!"
    />
    <div v-if="showAddTask">
      <AddTask @add-task="addTask" />
    </div>

    <Tasks
      @toggle-reminder="toggleReminder"
      @delete-task="deleteTask"
      :tasks="tasks"
    />
  </div>
</template>

<script>
import Header from './components/Header.vue'
import Tasks from './components/Tasks.vue'
import AddTask from './components/AddTask.vue'

export default {
  name: 'App',
  components: { Header, Tasks, AddTask },

  data() {
    return {
      tasks: [],
      showAddTask: false,
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
    toggleAddTask() {
      this.showAddTask = !this.showAddTask
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

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap');
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
body {
  font-family: 'Poppins', sans-serif;
}
.container {
  max-width: 500px;
  margin: 30px auto;
  overflow: auto;
  min-height: 300px;
  border: 1px solid steelblue;
  padding: 30px;
  border-radius: 5px;
}
.btn {
  display: inline-block;
  background: #000;
  color: #fff;
  border: none;
  padding: 10px 20px;
  margin: 5px;
  border-radius: 5px;
  cursor: pointer;
  text-decoration: none;
  font-size: 15px;
  font-family: inherit;
}
.btn:focus {
  outline: none;
}
.btn:active {
  transform: scale(0.98);
}
.btn-block {
  display: block;
  width: 100%;
}
</style>
