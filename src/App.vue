<template>
  <div :class="['app', { dark: darkMode }]">
    <h1>📅 Personal Timetable</h1>

    <!-- Dark Mode Toggle -->
    <button class="theme-btn" @click="toggleDarkMode">
      {{ darkMode ? "☀️ Light Mode" : "🌙 Dark Mode" }}
    </button>

    <button class="export-btn" @click="exportPDF">
      📄 Export Timetable as PDF
    </button>

    <!-- Task Input Form -->
    <div class="form">
      <input v-model="newDay" placeholder="Day (Monday)" />
      <input v-model="newTime" placeholder="Time (9:00 - 10:00)" />
      <input v-model="newTask" placeholder="Task name" />
      <button @click="addTask">
        {{ editingIndex !== null ? "Update Task" : "Add Task" }}
      </button>
    </div>

    <!-- Weekly Timetable Grid -->
    <div class="timetable" ref="timetable">
      <!-- Header -->
      <div class="header empty"></div>
      <div class="header" v-for="day in days" :key="day">
        {{ day }}
      </div>

      <!-- Rows -->
      <template v-for="time in timeSlots" :key="time">
        <div class="time">{{ time }}</div>

        <div class="cell" v-for="day in days" :key="day">
          <div v-if="getTask(day, time)">
            {{ getTask(day, time).name }}
            <br />

            <button
              class="edit-btn"
              @click="editTask(tasks.indexOf(getTask(day, time)))"
            >
              ✏️
            </button>

            <button
              class="delete-btn"
              @click="deleteTask(tasks.indexOf(getTask(day, time)))"
            >
              ❌
            </button>
          </div>
        </div>
      </template>
    </div>
  </div>
</template>

<script>
import html2pdf from "html2pdf.js";

export default {
  name: "App",

  data() {
    return {
      /* Tasks */
      tasks: [
        { day: "Monday", time: "9:00 - 10:00", name: "Study" },
        { day: "Tuesday", time: "10:00 - 11:00", name: "Exercise" },
      ],

      /* Form fields */
      newDay: "",
      newTime: "",
      newTask: "",

      /* Edit state */
      editingIndex: null,

      /* Dark mode */
      darkMode: false,

      /* Timetable structure */
      days: [
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
        "Sunday",
      ],

      timeSlots: [
        "9:00 - 10:00",
        "10:00 - 11:00",
        "11:00 - 12:00",
        "12:00 - 1:00",
      ],
    };
  },

  methods: {
    /* Add or Update task */
    addTask() {
      if (!this.newDay || !this.newTime || !this.newTask) {
        alert("Please fill all fields");
        return;
      }

      if (this.editingIndex !== null) {
        this.tasks[this.editingIndex] = {
          day: this.newDay,
          time: this.newTime,
          name: this.newTask,
        };
        this.editingIndex = null;
      } else {
        this.tasks.push({
          day: this.newDay,
          time: this.newTime,
          name: this.newTask,
        });
      }

      this.newDay = "";
      this.newTime = "";
      this.newTask = "";
    },
    exportPDF() {
      const element = this.$refs.timetable;

      const options = {
        margin: 0.5,
        filename: "My_Timetable.pdf",
        image: { type: "jpeg", quality: 0.98 },
        html2canvas: { scale: 2 },
        jsPDF: { unit: "in", format: "a4", orientation: "landscape" },
      };

      html2pdf().set(options).from(element).save();
    },

    /* Delete task */
    deleteTask(index) {
      this.tasks.splice(index, 1);
    },

    /* Edit task */
    editTask(index) {
      const task = this.tasks[index];
      this.newDay = task.day;
      this.newTime = task.time;
      this.newTask = task.name;
      this.editingIndex = index;
    },

    /* Find task in grid */
    getTask(day, time) {
      return this.tasks.find((task) => task.day === day && task.time === time);
    },

    /* Toggle dark mode */
    toggleDarkMode() {
      this.darkMode = !this.darkMode;
    },
  },

  /* Load saved data */
  mounted() {
    const savedTasks = localStorage.getItem("tasks");
    if (savedTasks) {
      this.tasks = JSON.parse(savedTasks);
    }

    const savedTheme = localStorage.getItem("darkMode");
    if (savedTheme) {
      this.darkMode = JSON.parse(savedTheme);
    }
  },

  /* Save data */
  watch: {
    tasks: {
      handler(newTasks) {
        localStorage.setItem("tasks", JSON.stringify(newTasks));
      },
      deep: true,
    },

    darkMode(newVal) {
      localStorage.setItem("darkMode", JSON.stringify(newVal));
    },
  },
};
</script>

<style>
.export-btn {
  margin-left: 10px;
  padding: 8px;
  cursor: pointer;
}

.app {
  max-width: 1000px;
  margin: auto;
  font-family: Arial, sans-serif;
  padding: 20px;
}

.theme-btn {
  margin-bottom: 15px;
}

.form {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

.form input {
  padding: 8px;
}

.form button {
  padding: 8px;
  cursor: pointer;
}

.timetable {
  display: grid;
  grid-template-columns: 120px repeat(7, 1fr);
  gap: 5px;
}

.header {
  font-weight: bold;
  background: #f0f0f0;
  text-align: center;
  padding: 10px;
}

.time {
  font-weight: bold;
  background: #f9f9f9;
  padding: 10px;
}

.cell {
  min-height: 60px;
  border: 1px solid #ddd;
  text-align: center;
  padding: 5px;
}

.edit-btn {
  background: orange;
  border: none;
  margin: 3px;
  cursor: pointer;
}

.delete-btn {
  background: red;
  color: white;
  border: none;
  margin: 3px;
  cursor: pointer;
}

/* Dark Mode */
.dark {
  background: #121212;
  color: white;
}

.dark input,
.dark button {
  background: #333;
  color: white;
  border: 1px solid #555;
}

.dark .header,
.dark .time {
  background: #222;
}

.dark .cell {
  border-color: #444;
}
</style>
