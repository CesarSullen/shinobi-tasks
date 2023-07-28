<script setup>
	import { ref, onMounted, computed, watch } from "vue";

	//variables
	const todos = ref([]);
	const name = ref("");
	const inputContent = ref("");
	const inputCategory = ref(null);

	//functions
	const todosAsc = computed(() =>
		todos.value.sort((a, b) => {
			return b.createdAt - a.createdAt;
		})
	);
	const addTodo = () => {
		if (inputContent.value.trim() === "" || inputCategory.value === null) {
			return;
		}
		todos.value.push({
			content: inputContent.value,
			category: inputCategory.value,
			done: false,
			createdAt: new Date().getTime(),
		});

		inputContent.value = "";
		inputCategory.value = null;
		document.querySelector(".bubbleBusiness").classList.remove("selected");
		document.querySelector(".bubblePersonal").classList.remove("selected");
	};
	const setBusiness = () => {
		inputCategory.value = "business";
		if (
			document.querySelector(".bubblePersonal").classList.contains("selected")
		) {
			document.querySelector(".bubblePersonal").classList.remove("selected");
			document.querySelector(".bubbleBusiness").classList.add("selected");
		} else {
			document.querySelector(".bubbleBusiness").classList.add("selected");
		}
	};
	const setPersonal = () => {
		inputCategory.value = "personal";
		if (
			document.querySelector(".bubbleBusiness").classList.contains("selected")
		) {
			document.querySelector(".bubbleBusiness").classList.remove("selected");
			document.querySelector(".bubblePersonal").classList.add("selected");
		} else {
			document.querySelector(".bubblePersonal").classList.add("selected");
		}
	};

	const removeTodo = (todo) => {
		todos.value = todos.value.filter((t) => t !== todo);
	};

	// local storage
	watch(name, (newVal) => {
		localStorage.setItem("name", newVal);
	});
	watch(
		todos,
		(newVal) => {
			localStorage.setItem("todos", JSON.stringify(newVal));
		},
		{ deep: true }
	);

	// lifecycle hooks
	onMounted(() => {
		name.value = localStorage.getItem("name") || "";
		todos.value = JSON.parse(localStorage.getItem("todos")) || [];
	});
</script>

<template>
	<main class="app">
		<section class="greeting">
			<h2 class="title">
				Ohayo,
				<input type="text" placeholder="Name here" v-model="name" />
			</h2>
			<img
				src="../../public/naruto.png"
				class="narutoImg"
				onclick="alert(`Dattebayo!`)"
			/>
		</section>
		<div class="interactiveContent">
			<section class="create-todo">
				<form @submit.prevent="addTodo">
					<h4>What are your missions for today?</h4>
					<input
						type="text"
						placeholder="e.g. meeting with the Hokage dattebayo"
						v-model="inputContent"
					/>
					<h4>Pick a category</h4>

					<div class="options">
						<span class="bubbleBusiness" @click="setBusiness">
							<input
								type="radio"
								name="category"
								value="business"
								v-model="inputCategory"
							/>
							Business
						</span>
						<span class="bubblePersonal" @click="setPersonal">
							<input
								type="radio"
								name="category"
								value="personal"
								v-model="inputCategory"
							/>
							Personal
						</span>
					</div>

					<input type="submit" value="Add mission" />
				</form>
			</section>
			<section class="todo-list">
				<h4>Missions List</h4>

				<div
					v-for="todo in todosAsc"
					:class="`todo-item ${todo.done && 'done'}`"
				>
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span class="check"></span>
					</label>

					<input type="text" v-model="todo.content" />
					<div class="selectedCategory">
						{{ todo.category }}
					</div>
					<button class="delete" @click="removeTodo(todo)">Delete</button>
				</div>
			</section>
		</div>
	</main>
</template>

<style></style>
