<script setup>
import { ref, reactive, onMounted, watch } from 'vue';

const newTodo = ref('');

// 初始化 max_id
let max_id = 0;

// 初始化为空数组，后续会从 localStorage 加载
const items = reactive([]);

// 从 localStorage 加载数据
function loadTodos() {
    const savedTodos = localStorage.getItem('todos');
    const savedMaxId = localStorage.getItem('max_id');
    
    if (savedTodos) {
        try {
            const parsedTodos = JSON.parse(savedTodos);
            items.splice(0, items.length, ...parsedTodos);
        } catch (error) {
            console.error('Failed to parse todos from localStorage:', error);
        }
    }
    
    if (savedMaxId) {
        max_id = parseInt(savedMaxId);
    }
}

// 保存数据到 localStorage
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(items));
    localStorage.setItem('max_id', max_id.toString());
}

function addTodo() {
    console.log('Adding todo:', newTodo.value);
    if (newTodo.value.trim() === '') {
        return;
    }
    items.push({
        id: ++max_id,
        text: newTodo.value,
        completed: false,
    });
    newTodo.value = '';
}

function removeTodo(id) {
    console.log('Removing todo with id:', id);
    const index = items.findIndex(item => item.id === id);
    if (index !== -1) {
        items.splice(index, 1);
    }
}

// 监听 items 的变化，保存到 localStorage
watch(items, () => {
    saveTodos();
}, { deep: true });

// 组件挂载时加载数据
onMounted(() => {
    loadTodos();
});
</script>

<template>
    <div id="app">
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
            <input type="text" v-model.trim="newTodo" placeholder="Add a new todo" @keyup.enter="addTodo" />
            <button @click="addTodo">Add</button>
        </div>
        <ul class="todoList">
                <li class="todoItem" v-for="item in items" :key="item.id" :class="{ completed: item.completed }">
                    <div class="itemFront">
                        <input type="checkbox" v-model="item.completed" />
                        <span>{{ item.text }}</span>
                    </div>
                    <button @click="removeTodo(item.id)" aria-label="Delete">
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
.topBar {
    width: 4em;
    display: flex;
    justify-content: space-between;
}

.topBar i {
    border-radius: 50%;
    width: 1em;
    height: 1em;
    padding: 1px;
    display: flex;
    justify-content: center;
    align-items: center;
    color: #000;
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

i.maximize svg {
    transform: scale(0.9);
}

.topBar i svg {
    opacity: 0;
    width: 100;
    height: auto;
}

.topBar:hover svg {
    opacity: 1;
}

h1 {
    margin: 0;
}

#app {
    max-width: 800px; /* 使用 CSS 变量 */
    min-width: 100px;
    margin: 0 auto; /* 默认居中 */
    margin-top: 20px;
    border-radius: 10px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
    background-color: #fff;
    color: #333;
    text-align: center;
    padding: 20px;
}

.inputBar {
    width: 100%;
    height: 40px;
    margin-top: 20px;
    padding: 0 10px;
    display: flex;
    justify-content: center;
}

.inputBar input {
    margin: 0;
    flex: 1;
    border: #333 1px solid;
    border-right: none;
    border-radius: 20px 0 0 20px;
    padding-left: 20px;
    outline: none;
}

.inputBar button {
    width: 4em;
    margin: 0;
    border: none;
    border-radius: 0 20px 20px 0;
    background-color: #f08a5d;
    color: #eaffd0;
    font-size: 18px;
    line-height: 40px;
    cursor: pointer;
    padding: 0 auto;
}

.todoList {
    /* border: #333 1px dashed; */
    padding: 20px;
    border-radius: 20px;
}

.todoItem {
    margin-top: 20px;
    width: 100%;
    border: #0000002e 2px dashed;
    box-shadow: 0 0 5px rgba(0, 0, 0, 0.3);
    background-color: #f4f1f1;
    height: 60px;
    border-radius: 15px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0 20px;
    transition: all 0.3s ease;
}

.todoItem.completed {
    /* 完成的任务 */
    text-decoration: line-through;
    filter: opacity(0.5);
}

.todoItem:hover {
    /* 大小变化 */
    transform: scale(1.02);
}

.todoItem:first-child {
    margin-top: 0;
}

.itemFront {
    display: flex;
    align-items: center;
}

.todoItem input[type="checkbox"] {
    width: 20px;
    height: 20px;
    margin-right: 10px;
    cursor: pointer;
}

.todoItem button {
    border: none;
    border-radius: 25%;
    cursor: pointer;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 0;
    height: 40px;
    width: 40px;
    background-color: #f4f1f1;
    transition: all 0.3s ease;
}

.todoItem button:hover {
    background-color: #dbdbdb;
}

/* 媒体查询：当页面宽度小于容器宽度时，设置左右至少 20px 的 margin */
@media (max-width: 840px) {
    #app {
        margin-left: 20px;
        margin-right: 20px;
    }
}

@media (prefers-color-scheme: dark) {
    #app {
        background-color: #1e1e1e;
        color: #e0e0e0;
        box-shadow: 0 0 10px rgba(255, 255, 255, 0.5);
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
