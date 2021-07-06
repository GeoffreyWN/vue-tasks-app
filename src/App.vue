<template>
  <div class="container">
    <Header title="TrackMyTasks!" />
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

export default {
  name: 'App',
  components: { Header, Tasks },

  data() {
    return {
      tasks: [],
    }
  },

  methods: {
    deleteTask(id) {
      if (confirm('Kindly confirm delete!')) {
        console.log('delete task', id)
        this.tasks = this.tasks.filter((task) => task.id !== id)
      }
    },

    toggleReminder(id) {
      console.log('toggle reminder'.id)
      this.tasks = this.tasks.map((task) =>
        task.id === id ? { ...task, reminder: !task.reminder } : task
      )
    },
  },

  created() {
    //register side effects
    this.tasks = [
      {
        id: 1,
        text: 'Visit Bahamas',
        day: 'March 22nd',
        reminder: true,
      },
      {
        id: 2,
        text: 'Call Messi',
        day: 'March 2nd',
        reminder: false,
      },
      {
        id: 3,
        text: 'Call Phil',
        day: 'Jan 1st',
        reminder: false,
      },
    ]
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
