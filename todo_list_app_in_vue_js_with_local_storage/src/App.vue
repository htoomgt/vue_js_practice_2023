<script setup>
import { ref, onMounted, computed, watch } from "vue";
import uniqid from "uniqid";

const todos = ref([]);
const name = ref("");

const input_content = ref("");
const input_category = ref(null);

const todos_asc = computed(() =>
    todos.value.sort((a, b) => {
        return b.createdAt - a.createdAt;
    })
);

const addTodo = () => {
    if (input_content.value.trim() === "" || input_category.value === null) {
        return;
    }

    console.log("addTodo");

    todos.value.push({
        id: uniqid(),
        content: input_content.value,
        category: input_category.value,
        done: false,
        createdAt: new Date().getTime(),
    });

    input_content.value = "";
    input_category.value = null;
};

const removeTodo = (id) => {
    console.log("removeTodo");

    todos.value = todos.value.filter((todo) => todo.id !== id);
};

const toggleTodoDone = (id) => {
    console.log("toggleTodoDone");

    todos.value = todos.value.map((todo) => {
        if (todo.id === id) {
            todo.done = !todo.done;
        }

        return todo;
    });
};

watch(
    todos,
    (newVal) => {
        localStorage.setItem("todos", JSON.stringify(newVal));
    },
    { deep: true }
);

watch(name, (newVal) => {
    localStorage.setItem("name", newVal);
});

onMounted(() => {
    name.value = localStorage.getItem("name") || "";
    todos.value = localStorage.getItem("todos")
        ? JSON.parse(localStorage.getItem("todos"))
        : [];
});
</script>

<template>
    <main class="app">
        <section class="greeting">
            <h2 class="title">
                What's up,
                <input type="text" placeholder="name here" v-model="name" />?
            </h2>
        </section>

        <section class="create-todo">
            <h3>CREATE A TODO</h3>

            <form @submit.prevent="addTodo">
                <h4>What's on your todo list?</h4>
                <input
                    type="text"
                    v-model="input_content"
                    placeholder="e.g. make a video"
                />
                <h4>Pick a category</h4>
                <div class="options">
                    <label for="category1">
                        <input
                            type="radio"
                            name="category"
                            id="category1"
                            value="business"
                            v-model="input_category"
                        />
                        <span class="bubble business"></span>
                        <div>Business</div>
                    </label>
                    <label for="category2">
                        <input
                            type="radio"
                            name="category"
                            id="category2"
                            value="personal"
                            v-model="input_category"
                        />
                        <span class="bubble personal"></span>
                        <div>Personal</div>
                    </label>
                </div>

                <input type="submit" value="Add to do" />
            </form>
        </section>

        <section class="todo-list">
            <h3>Todo List</h3>
            <div class="list">
                <div
                    v-for="todo in todos_asc"
                    :class="`todo-item ${todo.done && 'done'}`"
                    :key="todo.id"
                >
                    <label for="" @click="toggleTodoDone(todo.id)">
                        <input type="checkbox" v-model="todo.done" />
                        <span :class="`bubble ${todo.category}`"></span>
                    </label>
                    <div class="todo-content">
                        <input type="text" v-model="todo.content" />
                    </div>

                    <div class="actions">
                        <button class="delete" @click="removeTodo(todo.id)">
                            Delete
                        </button>
                    </div>
                </div>
            </div>
        </section>
    </main>
</template>
