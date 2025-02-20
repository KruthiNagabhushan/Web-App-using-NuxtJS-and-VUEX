<template>
    <div class="todo-app">
      <div class="todo-container">
        <h1 class="todo-title">To-Do List</h1>
  
        <!-- Add Task Section -->
        <div class="todo-input-container">
          <input
            v-model="newTask.text"
            placeholder="Add a new task"
            @keyup.enter="addTask"
            class="todo-input"
          />
          <select v-model="newTask.category" class="todo-category">
            <option value="Work">Work</option>
            <option value="Personal">Personal</option>
            <option value="Shopping">Shopping</option>
          </select>
          <input
            type="date"
            v-model="newTask.dueDate"
            class="todo-due-date"
          />
          <button @click="addTask" class="todo-add-button">Add</button>
        </div>
  
        <!-- Search Bar -->
        <input
          v-model="searchQuery"
          placeholder="Search tasks"
          class="todo-search"
        />
  
        <!-- Filter Buttons -->
        <div class="todo-filters">
          <button @click="filter = 'all'" :class="{ active: filter === 'all' }">All</button>
          <button @click="filter = 'completed'" :class="{ active: filter === 'completed' }">Completed</button>
          <button @click="filter = 'incomplete'" :class="{ active: filter === 'incomplete' }">Incomplete</button>
        </div>
  
        <!-- Task List -->
        <ul class="todo-list">
          <li
            v-for="(task, index) in filteredAndSearchedTasks"
            :key="index"
            class="todo-item"
            :class="{ completed: task.completed }"
          >
            <input
              type="checkbox"
              v-model="task.completed"
              class="todo-checkbox"
            />
            <span class="task-text">
              {{ task.text }} ({{ task.category }}) - Due: {{ task.dueDate }}
            </span>
            <button @click="removeTask(index)" class="todo-remove-button">×</button>
          </li>
        </ul>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, computed, onMounted } from 'vue';
  
  // Task Data
  const tasks = ref([]);
  const newTask = ref({
    text: '',
    category: 'Work',
    dueDate: '',
    completed: false,
  });
  
  // Search and Filter
  const searchQuery = ref('');
  const filter = ref('all');
  
  // Add Task
  const addTask = () => {
    if (newTask.value.text.trim() !== '') {
      tasks.value.push({ ...newTask.value });
      newTask.value.text = '';
      newTask.value.dueDate = '';
      saveTasks();
    }
  };
  
  // Remove Task
  const removeTask = (index) => {
    tasks.value.splice(index, 1);
    saveTasks();
  };
  
  // Filtered and Searched Tasks
  const filteredAndSearchedTasks = computed(() => {
    let filteredTasks = tasks.value;
  
    // Filter by completion status
    if (filter.value === 'completed') {
      filteredTasks = tasks.value.filter(task => task.completed);
    } else if (filter.value === 'incomplete') {
      filteredTasks = tasks.value.filter(task => !task.completed);
    }
  
    // Filter by search query
    if (searchQuery.value) {
      filteredTasks = filteredTasks.filter(task =>
        task.text.toLowerCase().includes(searchQuery.value.toLowerCase())
      );
    }
  
    return filteredTasks;
  });
  
  // Local Storage
  const saveTasks = () => {
    localStorage.setItem('tasks', JSON.stringify(tasks.value));
  };
  
  const loadTasks = () => {
    const savedTasks = localStorage.getItem('tasks');
    if (savedTasks) {
      tasks.value = JSON.parse(savedTasks);
    }
  };
  
  // Load tasks on mount
  onMounted(() => {
    loadTasks();
  });
  </script>
  
  <style scoped>
  /* General Styles */
  .todo-app {
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    background-color: #f5f5f5;
    font-family: 'Arial', sans-serif;
  }
  
  .todo-container {
    background: white;
    border-radius: 10px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    padding: 20px;
    width: 100%;
    max-width: 500px;
  }
  
  .todo-title {
    text-align: center;
    color: #333;
    margin-bottom: 20px;
  }
  
  /* Input Container */
  .todo-input-container {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
  }
  
  .todo-input,
  .todo-category,
  .todo-due-date,
  .todo-search {
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 5px;
    font-size: 14px;
    outline: none;
  }
  
  .todo-input {
    flex: 1;
  }
  
  .todo-add-button {
    padding: 10px 20px;
    background-color: #007bff;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    font-size: 14px;
    transition: background-color 0.3s;
  }
  
  .todo-add-button:hover {
    background-color: #0056b3;
  }
  
  /* Search Bar */
  .todo-search {
    width: 100%;
    margin-bottom: 20px;
  }
  
  /* Filter Buttons */
  .todo-filters {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
  }
  
  .todo-filters button {
    padding: 10px 20px;
    background-color: #f0f0f0;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    font-size: 14px;
    transition: background-color 0.3s;
  }
  
  .todo-filters button.active {
    background-color: #007bff;
    color: white;
  }
  
  .todo-filters button:hover {
    background-color: #ddd;
  }
  
  /* Task List */
  .todo-list {
    list-style-type: none;
    padding: 0;
    margin: 0;
  }
  
  .todo-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px;
    border-bottom: 1px solid #eee;
    transition: background-color 0.3s;
  }
  
  .todo-item:last-child {
    border-bottom: none;
  }
  
  .todo-item:hover {
    background-color: #f9f9f9;
  }
  
  .todo-checkbox {
    margin-right: 10px;
  }
  
  .task-text {
    flex: 1;
    font-size: 16px;
    color: #333;
  }
  
  .completed .task-text {
    text-decoration: line-through;
    color: #888;
  }
  
  .todo-remove-button {
    background: none;
    border: none;
    color: #ff4d4d;
    font-size: 20px;
    cursor: pointer;
    transition: color 0.3s;
  }
  
  .todo-remove-button:hover {
    color: #cc0000;
  }
  </style>