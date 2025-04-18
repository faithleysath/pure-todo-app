<script setup>
import { ref, reactive, watch, onMounted } from "vue";



const newTodo = ref('');
const todos = reactive([]);
let maxId = 0;
const todoInputRef = ref(null);

function loadTodos() {
    const savedTodos = localStorage.getItem('todos');
    const savedMaxId = localStorage.getItem('max_id');

    if (savedTodos) {
        try {
            const parsedTodos = JSON.parse(savedTodos);
            todos.splice(0, todos.length, ...parsedTodos);
        } catch (error) {
            console.error(error);
        }
    }

    if (savedMaxId) {
        maxId = parseInt(savedMaxId);
    }
}

function savedTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
    localStorage.setItem('max_id', maxId.toString());
}

function addTodo() {
    if (!newTodo.value) {
        todoInputRef.value.focus();
        let blinkCount = 0;
        const interval = setInterval(() => {
            todoInputRef.value.style.outline = blinkCount % 2 === 0 ? "1px solid red" : "none";
            blinkCount++;
            if (blinkCount === 8) {
            clearInterval(interval);
            todoInputRef.value.style.outline = "none";
            }
        }, 100);
        return;
    }
    todos.push({
        id: ++maxId,
        text: newTodo.value,
        completed: false
    });
    newTodo.value = '';
}

function removeTodo(id) {
    const index = todos.findIndex(todo => todo.id === id);
    if (index !== -1) {
        todos.splice(index, 1);
    }
}

watch(todos, savedTodos, {deep: true});

onMounted(loadTodos);

</script>

<template>
    <div class="app">
        <div class="topBar">
            <i class="close">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <line x1="18" y1="6" x2="6" y2="18"></line>
                    <line x1="6" y1="6" x2="18" y2="18"></line>
                </svg>
            </i>
            <i class="minimize">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <line x1="4" y1="12" x2="20" y2="12"></line>
                </svg>
            </i>
            <i class="maximize">
                <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                    <path d="M8 3H5a2 2 0 0 0-2 2v3"></path>
                    <path d="M16 3h3a2 2 0 0 1 2 2v3"></path>
                    <path d="M16 21h3a2 2 0 0 0 2-2v-3"></path>
                    <path d="M8 21H5a2 2 0 0 1-2-2v-3"></path>
                </svg>
            </i>
        </div>
        <h1>Todo App</h1>
        <div class="inputBar">
            <input type="text" class="todoInput" placeholder="input a new todo..." v-model.trim="newTodo" ref="todoInputRef">
            <button class="addTodoBtn" @click="addTodo">Add Todo</button>
        </div>
        <ul class="todoList">
            <li class="todoItem" v-for="todo in todos" :key="todo.id" :class="{ completed: todo.completed}" >
                <div class="todoFront">
                    <input type="checkbox" v-model="todo.completed">
                    <span>{{ todo.text }}</span>
                </div>
                <button @click="removeTodo(todo.id)" aria-label="Delete">
                    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                            <polyline points="3 6 5 6 21 6"></polyline>
                            <path d="M19 6l-1.5 14h-11L5 6"></path>
                            <path d="M10 11v6"></path>
                            <path d="M14 11v6"></path>
                            <path d="M9 4h6v2H9z"></path>
                        </svg> 
                </button>
            </li>
        </ul>
    </div>
</template>

<style scoped>

h1 {
    margin: 0;
}

.topBar {
    position: absolute;
    top: 10px;
    left: 10px;
    display: flex;
    width: 4em;
    justify-content: space-between;
}

.topBar i {
    width: 1em;
    height: 1em;
    padding: 1px;
    display: flex;
    border-radius: 50%;
}

.topBar i.close {
    background-color: rgb(255, 95, 87);
}

.topBar i.minimize {
    background-color: rgb(254, 188, 46);
}

.topBar i.maximize {
    background-color: rgb(40, 200, 64);
}

.topBar svg {
    opacity: 0;
    width: 100%;
    height: auto;
    transform: scale(0.9);
}

.topBar:hover svg {
    opacity: 1;
}

.app {
    max-width: 800px;
    background-color: #fff;
    margin: 0 auto;
    margin-top: 20px;
    padding: 20px 40px;
    border-radius: 10px;
    box-shadow: 0 0 10px rgba(0,0,0,0.5);
    display: flex;
    flex-direction: column;
    align-items: center;
    position: relative;
}

.inputBar {
    margin: 20px;
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100%;
}

.inputBar input {
    flex: 1;
    height: 40px;
    border: #333 1px solid;
    outline: none;
    border-radius: 18px 0 0 18px;
    padding-left: 20px;
}

.inputBar button {
    border: none;
    height: 40px;
    width: 5.5em;
    border-radius: 0 18px 18px 0;
    background-color: #f08a5d;
    color: #f2fae8;
    cursor: pointer;
}

ul {
    list-style: none;
    padding: 0;
}
.todoList {
    width: 100%;
}

.todoItem {
    margin-top: 20px;
    display: flex;
    width: 100%;
    border: #0000002e 2px dashed;
    box-shadow: 0 0 5px rgba(0,0,0,0.3);
    background-color: #f4f1f1;
    align-items: center;
    justify-content: space-between;
    height: 50px;
    padding: 0 20px;
    transition: all 0.3s ease;
}

.todoItem:first-child {
    margin-top: 0;
}

.todoItem:hover {
    transform: scale(1.02);
}

.todoItem.completed {
    text-decoration: line-through;
    filter: opacity(0.5);
}

.todoFront {
    display: flex;
    align-items: center;
}

.todoItem input[type="checkbox"] {
    width: 20px;
    height: 20px;
    margin-right: 10px;
}

.todoItem button {
    height: 40px;
    width: 40px;
    display: flex;
    justify-content: center;
    align-items: center;
    border-radius: 25%;
    background-color: inherit;
    border: none;
    cursor: pointer;
    transition: all 0.3s ease;
}

.todoItem button:hover {
    background-color: #dbdbdb;
}


@media (max-width: 840px) {
    .app {
        margin: 0 20px;
        margin-top: 20px;
    }
}

@media (prefers-color-scheme: dark) {
    .app {
        background-color: #1e1e1e;
        color: #e0e0e0;
        box-shadow: 0 0 10px rgba(255, 255, 255, 0.5);
    }
    
    .topBar {
        color: #000000;
    }

    .inputBar input {
        background-color: #333;
        color: #e0e0e0;
        border: #555 1px solid;
    }

    .inputBar button {
        background-color: #07a3e6;        ;
        color: #000000;
    }

    .todoItem {
        background-color: #333;
        border: #555 2px dashed;
        box-shadow: 0 0 5px rgba(255, 255, 255, 0.3);
    }

    .todoItem button {
        background-color: #333;
    }

    .todoItem button:hover {
        background-color: #555;
    }
}

</style>
