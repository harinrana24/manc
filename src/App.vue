<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const userName = ref('')

const inputContent = ref('')
const inputCategory = ref(null)

const sortedTodos = computed(() => todos.value.sort((a, b) => a.createdAt - b.createdAt))

watch(userName, (newVal) => {
	localStorage.setItem('userName', newVal)
})

watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})

const addTodo = () => {
	if (!inputContent.value.trim() || !inputCategory.value) {
		return
	}

	todos.value.push({
		content: inputContent.value,
		category: inputCategory.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})
}

const deleteTodo = (todo) => {
	todos.value = todos.value.filter(t => t !== todo)
}

onMounted(() => {
	userName.value = localStorage.getItem('userName') || ''
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
	<main class="app">
		<section class="greeting">
			<h2 class="title">Hello, <input type="text" id="userName" placeholder="Your name" v-model="userName"></h2>
		</section>

		<section class="todo-creation">
			<h3>Create Your Tasks</h3>
			<form @submit.prevent="addTodo">
				<h4>Task Details</h4>
				<input type="text" id="content" placeholder="Task description" v-model="inputContent">
				
				<h4>Category</h4>
				<div class="category-select">
					<label>
						<input type="radio" name="category" value="Business" v-model="inputCategory">
						<span class="bubble business"></span>
						<span>Business</span>
					</label>
					<label>
						<input type="radio" name="category" value="Personal" v-model="inputCategory">
						<span class="bubble personal"></span>
						<span>Personal</span>
					</label>
				</div>
				<button type="submit">Add Task</button>
			</form>
		</section>

		<section class="task-list">
			<h3>Tasks</h3>
			<div>
				<div v-for="todo in sortedTodos" :key="todo.createdAt" :class="{'task-item': true, 'completed': todo.done}">
					<input type="checkbox" v-model="todo.done">
					<span :class="`bubble ${todo.category.toLowerCase()}`"></span>
					<input type="text" v-model="todo.content">
					<button class="delete" @click="deleteTodo(todo)">Delete</button>
				</div>
			</div>
		</section>
	</main>
</template>
