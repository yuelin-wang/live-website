<template>
  <div class="todo-app">
    <h1>My To-Do List</h1>
    
    <!-- Add new todo form -->
    <div class="add-todo">
      <input 
        v-model="newTodo" 
        @keyup.enter="addTodo"
        placeholder="What needs to be done?"
        class="todo-input"
      >
      <button @click="addTodo" class="add-btn">Add</button>
    </div>

    <!-- Filter buttons -->
    <div class="filters">
      <button 
        @click="filter = 'all'" 
        :class="{ active: filter === 'all' }"
      >
        All ({{ todos.length }})
      </button>
      <button 
        @click="filter = 'active'" 
        :class="{ active: filter === 'active' }"
      >
        Active ({{ activeTodosCount }})
      </button>
      <button 
        @click="filter = 'completed'" 
        :class="{ active: filter === 'completed' }"
      >
        Completed ({{ completedTodosCount }})
      </button>
    </div>

    <!-- Todo list -->
    <div class="todo-list">
      <div 
        v-for="todo in filteredTodos" 
        :key="todo.id" 
        class="todo-item"
        :class="{ completed: todo.completed }"
      >
        <input 
          type="checkbox" 
          v-model="todo.completed" 
          class="todo-checkbox"
        >
        <span class="todo-text">{{ todo.text }}</span>
        <button @click="removeTodo(todo.id)" class="delete-btn">×</button>
      </div>
      
      <div v-if="filteredTodos.length === 0" class="empty-state">
        No todos found
      </div>
    </div>

    <!-- Clear completed button -->
    <div v-if="completedTodosCount > 0" class="clear-completed">
      <button @click="clearCompleted" class="clear-btn">
        Clear Completed ({{ completedTodosCount }})
      </button>
    </div>
  </div>
</template>

<script>
import { ref, computed } from 'vue'

export default {
  name: 'TodoApp',
  setup() {
    const todos = ref([])
    const newTodo = ref('')
    const filter = ref('all')

    // Add new todo
    const addTodo = () => {
      if (newTodo.value.trim()) {
        todos.value.push({
          id: Date.now(),
          text: newTodo.value.trim(),
          completed: false
        })
        newTodo.value = ''
      }
    }

    // Remove todo
    const removeTodo = (id) => {
      todos.value = todos.value.filter(todo => todo.id !== id)
    }

    // Clear completed todos
    const clearCompleted = () => {
      todos.value = todos.value.filter(todo => !todo.completed)
    }

    // Computed properties
    const activeTodosCount = computed(() => 
      todos.value.filter(todo => !todo.completed).length
    )

    const completedTodosCount = computed(() => 
      todos.value.filter(todo => todo.completed).length
    )

    const filteredTodos = computed(() => {
      switch (filter.value) {
        case 'active':
          return todos.value.filter(todo => !todo.completed)
        case 'completed':
          return todos.value.filter(todo => todo.completed)
        default:
          return todos.value
      }
    })

    return {
      todos,
      newTodo,
      filter,
      addTodo,
      removeTodo,
      clearCompleted,
      activeTodosCount,
      completedTodosCount,
      filteredTodos
    }
  }
}
</script>

<style scoped>
.todo-app {
  max-width: 500px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

h1 {
  text-align: center;
  color: #333;
  margin-bottom: 30px;
}

.add-todo {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

.todo-input {
  flex: 1;
  padding: 12px;
  border: 2px solid #ddd;
  border-radius: 6px;
  font-size: 16px;
}

.todo-input:focus {
  outline: none;
  border-color: #4CAF50;
}

.add-btn {
  padding: 12px 20px;
  background: #4CAF50;
  color: white;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-size: 16px;
}

.add-btn:hover {
  background: #45a049;
}

.filters {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
  justify-content: center;
}

.filters button {
  padding: 8px 16px;
  border: 1px solid #ddd;
  background: white;
  border-radius: 4px;
  cursor: pointer;
}

.filters button.active {
  background: #4CAF50;
  color: white;
  border-color: #4CAF50;
}

.todo-list {
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 2px 10px rgba(0,0,0,0.1);
}

.todo-item {
  display: flex;
  align-items: center;
  padding: 15px;
  border-bottom: 1px solid #eee;
  background: white;
  transition: background-color 0.2s;
}

.todo-item:hover {
  background: #f9f9f9;
}

.todo-item:last-child {
  border-bottom: none;
}

.todo-item.completed {
  opacity: 0.7;
}

.todo-checkbox {
  margin-right: 12px;
  transform: scale(1.2);
}

.todo-text {
  flex: 1;
  font-size: 16px;
}

.todo-item.completed .todo-text {
  text-decoration: line-through;
  color: #888;
}

.delete-btn {
  background: #ff4444;
  color: white;
  border: none;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  cursor: pointer;
  font-size: 18px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.delete-btn:hover {
  background: #cc0000;
}

.empty-state {
  text-align: center;
  padding: 40px;
  color: #888;
  font-style: italic;
}

.clear-completed {
  margin-top: 20px;
  text-align: center;
}

.clear-btn {
  padding: 10px 20px;
  background: #ff6b6b;
  color: white;
  border: none;
  border-radius: 6px;
  cursor: pointer;
}

.clear-btn:hover {
  background: #ff5252;
}
</style>