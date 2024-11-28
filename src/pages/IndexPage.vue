<template>
  <q-page class="q-pa-md" style="max-width: 900px; margin: 0 auto;">
    <!-- Task Creation Section -->
    <div class="q-mb-md">
      <q-input
        filled
        v-model="newTask"
        label="Enter New Task"
        @keyup.enter="addTask"
        :loading="loading"
        class="q-mb-md"
        color="primary"
      >
        <template v-slot:append>
          <q-btn round flat icon="add" @click="addTask" color="primary" />
        </template>
      </q-input>
    </div>

    <!-- Pending Tasks Section -->
    <div v-if="pendingTasks.length" class="q-mb-md">
      <div class="text-h6 text-primary q-mb-md">Pending Tasks</div>
      <div v-for="task in pendingTasks" :key="task.id" class="q-card q-mb-md shadow-2">
        <q-card>
          <q-card-section>
            <div class="text-h6">{{ task.title }}</div>
            <div class="text-subtitle2 text-grey-7">Status: Pending</div>
          </q-card-section>

          <q-card-actions>
            <q-btn
              flat
              icon="check_circle"
              color="primary"
              label="Mark as Completed"
              @click="markCompleted(task.id)"
            />
            <q-btn
              flat
              icon="delete"
              color="negative"
              label="Delete"
              @click="deleteTask(task.id)"
            />
          </q-card-actions>
        </q-card>
      </div>
    </div>

    <!-- Completed Tasks Section -->
    <div v-if="completedTasks.length" class="q-mb-md">
      <div class="text-h6 text-positive q-mb-md">Completed Tasks</div>
      <div v-for="task in completedTasks" :key="task.id" class="q-card q-mb-md shadow-2">
        <q-card>
          <q-card-section>
            <div class="text-h6">{{ task.title }}</div>
            <div class="text-subtitle2 text-grey-7">Status: Completed</div>
          </q-card-section>

          <q-card-actions>
            <q-btn
              flat
              icon="delete"
              color="negative"
              label="Delete"
              @click="deleteTask(task.id)"
            />
          </q-card-actions>
        </q-card>
      </div>
    </div>

    <!-- No Tasks Message -->
    <div v-else class="q-mt-md text-center text-h6">
      No tasks found, add a new task!
    </div>

    <!-- Loading Spinner -->
    <q-spinner-dots v-if="loading" size="50px" color="primary" class="q-mt-md" />
  </q-page>
</template>

<script>
export default {
  data() {
    return {
      newTask: '',
      tasks: [],
      loading: false,
    };
  },
  computed: {
    // Filter tasks into pending and completed
    pendingTasks() {
      return this.tasks.filter((task) => !task.completed);
    },
    completedTasks() {
      return this.tasks.filter((task) => task.completed);
    },
  },
  methods: {
    // Add Task
    addTask() {
      if (!this.newTask.trim()) return;

      this.loading = true;

      const newTaskData = {
        id: Date.now(),
        title: this.newTask,
        completed: false,
      };

      this.tasks.push(newTaskData);
      this.saveTasksToLocalStorage();
      this.newTask = '';
      this.loading = false;
    },

    // Mark Task as Completed
    markCompleted(taskId) {
      this.loading = true;

      const task = this.tasks.find((task) => task.id === taskId);
      if (task) {
        task.completed = true;
        this.saveTasksToLocalStorage();
      }

      this.loading = false;
    },

    // Delete Task
    deleteTask(taskId) {
      this.loading = true;

      this.tasks = this.tasks.filter((task) => task.id !== taskId);
      this.saveTasksToLocalStorage();

      this.loading = false;
    },

    // Save tasks to localStorage
    saveTasksToLocalStorage() {
      localStorage.setItem('tasks', JSON.stringify(this.tasks));
    },

    // Load tasks from localStorage
    loadTasksFromLocalStorage() {
      const savedTasks = localStorage.getItem('tasks');
      if (savedTasks) {
        this.tasks = JSON.parse(savedTasks);
      }
    },
  },

  created() {
    this.loadTasksFromLocalStorage();
  },
};
</script>

<style scoped>
.q-card {
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.q-card:hover {
  transform: scale(1.05);
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
}

.q-btn {
  min-width: 130px;
}

.text-h6 {
  font-weight: 600;
}

.text-subtitle2 {
  font-size: 0.9rem;
}
</style>
