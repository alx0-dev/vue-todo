<script setup>
import { ref, watch, computed } from "vue";
import { uid } from "uid";
import { Icon } from "@iconify/vue";
import CreateTodo from "../components/CreateTodo.vue";
import TodoItem from "../components/TodoItem.vue";

const todoList = ref([]);

const setTodoListLocalStorage = () => {
    localStorage.setItem("todoList", JSON.stringify(todoList.value));
};

watch(todoList, setTodoListLocalStorage, {
    deep: true,
});

const todoListCompleted = computed(() => {
    return todoList.value.every((todo) => todo.isCompleted);
});

const fetchTodoList = () => {
    const savedTodoList = JSON.parse(localStorage.getItem("todoList"));
    if (savedTodoList) {
        todoList.value = savedTodoList;
    }
};

fetchTodoList();

const addTodo = (todo) => {
    todoList.value.push({
        id: uid(),
        todo,
        isCompleted: false,
        isEditing: null,
    });
};

const toggleTodoComplete = (index) => {
    todoList.value[index].isCompleted = !todoList.value[index].isCompleted;
};

const toggleEdit = (index) => {
    todoList.value[index].isEditing = !todoList.value[index].isEditing;
};

const updateTodo = (todo, index) => {
    todoList.value[index].todo = todo;
};

const deleteTodo = (id) => {
    todoList.value = todoList.value.filter((todo) => todo.id !== id);
};
</script>

<template>
    <main>
        Add new todo
        <CreateTodo @add-todo="addTodo" />
        <ul v-if="todoList.length" class="todo-list">
            <TodoItem
                v-for="(todo, index) in todoList"
                :todo="todo"
                :index="index"
                @toggle-complete="toggleTodoComplete"
                @toggle-edit="toggleEdit"
                @update-todo="updateTodo"
                @delete-todo="deleteTodo"
            />
        </ul>
        <p v-else class="todo-msg">
            <Icon icon="noto-v1:sad-but-relieved-face" width="22" />
            <span>No todos to complete.</span>
        </p>
        <p v-if="todoListCompleted && todoList.length" class="todo-msg">
            <Icon icon="noto-v1:party-popper" />
            <span>You have completed all your todos!</span>
        </p>
    </main>
</template>

<style lang="scss" scoped>
main {
    display: flex;
    flex-direction: column;
    max-width: 500px;
    width: 100%;
    margin: 0 auto;
    padding: 40px 16px;

    h1 {
        margin-bottom: 16px;
        text-align: center;
    }

    .todo-list {
        display: flex;
        flex-direction: column;
        list-style: none;
        margin-top: 24px;
        gap: 20px;
    }

    .todo-msg {
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 8px;
        margin-top: 24px;
    }
}
</style>
