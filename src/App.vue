<template>
  <div class="app">
    <h1>📅 Personal Timetable</h1>
    <div class="form">
      <input v-model="newDay" placeholder="Day ( Monday)" />
      <input v-model="newTime" placeholder="Time ( 9:00 - 10:00)" />
      <input v-model="newTask" placeholder="Task name" />
      <button @click="addTask">Add Task</button>
    </div>

    <ul>
      <li v-for="(task, index) in tasks" :key="index">
        {{ task.day }} | {{ task.time }} | {{ task.name }}
        <button @click="deleteTask(index)">Delete</button>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      tasks: [
        {
          day: 'Monday',
          time: '9:00 - 10:00',
          name: 'Study',
        },
        {
          day: 'Tuesday',
          time: '10:00 - 11:00',
          name: 'Exercise',
        },
      ],
      newDay: '',
      newTime: '',
      newTask: '',
    }
  },
  methods: {
    addTask() {
      if (this.newDay && this.newTime && this.newTask) {
        this.tasks.push({
          day: this.newDay,
          time: this.newTime,
          name: this.newTask,
        })

        this.newDay = ''
        this.newTime = ''
        this.newTask = ''
      } else {
        alert('Please fill all fields')
      }
    },

    deleteTask(index) {
      this.tasks.splice(index, 1)
    },
  },

  mounted() {
  const savedTasks = localStorage.getItem("tasks");

  if (savedTasks) {
    this.tasks = JSON.parse(savedTasks);
  }
},
watch: {
  tasks: {
    handler(newTasks) {
      localStorage.setItem("tasks", JSON.stringify(newTasks));
    },
    deep: true,
  },
},


}
</script>

<style>
.app {
  text-align: center;
  font-family: Arial, sans-serif;
  margin-top: 40px;
}
ul {
  list-style: none;
  padding: 0;
}

li {
  background: #f2f2f2;
  margin: 8px;
  padding: 10px;
  border-radius: 5px;
}
.form {
  margin-bottom: 20px;
}

input {
  margin: 5px;
  padding: 6px;
}

button {
  padding: 6px 10px;
  cursor: pointer;
}
li button {
  margin-left: 10px;
  background: red;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

</style>
