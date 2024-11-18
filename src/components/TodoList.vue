<template>
    <div class="todolist">
        <div class="todo_wrapper">
            <h1 style="border-bottom:1px dotted">To Do List</h1>
            <br>
            <div class="add_block">
                <input v-model="content" class="todo_input" type="text" placeholder="請輸入文字" />
                <button @click="sendText()" class="add_item">加入</button>
            </div>
            <div class="nav_block">
                <button v-for="item in navitem" :class="{ 'active': action == item.value }" class="nav_item"
                    @click="action = item.value">{{ item.name }}</button>
            </div>
            <ul v-if="filtertodo.length > 0" class="todo_item">
                <li v-for="todo in filtertodo" :key="todo.id" @dblclick="editTodo(todo)">
                    <button @click.stop="changeTodo(todo.id)" v-if="todo.status" class="btn_icon" type="button"
                        style="float:left">&#10004;</button> <!--完成button-->
                    <button @click.stop="changeTodo(todo.id)" v-else v-if="!todo.edit" type="button" class="btn_icon"
                        style="float:left">&#9744;</button> <!--未完成button-->
                    <input class="todo_input_edit" type="text" v-if="todo.edit" v-model="todo.temptext" />
                    <label :class="{ 'line-through': todo.status }" v-else
                        style="margin-left: 5px; cursor: pointer;">{{ todo.text }}</label>
                    <span v-if="todo.edit" style="float:right">
                        <!--編輯狀態下的button-->
                        <button @click.stop="saveTodo(todo.id)" class="btn_icon" type="button"
                            style="color:green;margin-right:5px">&#10004;</button> <!--存檔button-->
                        <button @click.stop="cancleTodo(todo.id)" class="btn_icon" type="button"
                            style="color:darkred">&#10008;</button> <!--取消button-->
                    </span>
                    <button @click.stop="deleteTodo(todo.id)" v-else class="btn_icon" type="button"
                        style="float:right">&#10008;</button> <!--刪除button-->
                </li>
            </ul>
            <ul class="todo_item" v-else>
                <li>沒有資料</li>
            </ul>
        </div>
    </div>
</template>

<script setup>
import { ref, computed } from 'vue'
const num = ref(0) //作為todolist中的id計數
const content = ref('') //和v-model進行介面input輸入框的資料雙向綁定
const todos = ref([])
const action = ref("all")
const navitem = ref([
    { name: "全部", value: "all" },
    { name: "進行中", value: "now" },
    { name: "已完成", value: "pass" }
])
const filtertodo = computed(function () {
    switch (action.value) {
        case "all": {
            return todos.value.filter(todo => true)
        }
        case "now": {
            return todos.value.filter(todo => !todo.status)
        }
        case "pass": {
            return todos.value.filter(todo => todo.status)
        }
        default: {
            return todos.value
        }
    }
})
function sendText() {
    if (content.value != "") {
        num.value += 1
        const todo = {
            id: num.value,
            text: content.value,
            temptext: content.value,
            status: false,
            edit: false
        }
        todos.value.push(todo)
        content.value = ""
    }
}
function deleteTodo(id) {
    todos.value = todos.value.filter(todo => todo.id !== id)
}
function changeTodo(id) {
    todos.value = todos.value.map(todo => todo.id === id ? { ...todo, status: !todo.status } : todo)
}
function editTodo(todo) {
    if (!todo.edit && !todo.status) //尚未編輯且尚未完成才會觸發
        todos.value = todos.value.map(onetodo => onetodo.id === todo.id ? { ...onetodo, edit: !onetodo.edit } : onetodo)
}
function cancleTodo(id) {
    todos.value = todos.value.map(todo => todo.id === id ? { ...todo, temptext: todo.text, edit: !todo.edit } : todo)
}
function saveTodo(id) {
    todos.value = todos.value.map(todo => todo.id === id ? { ...todo, text: todo.temptext, edit: !todo.edit } : todo)
}

</script>

<style>
@media (min-width: 1024px) {
    .todolist {
        min-height: 100vh;
        display: flex;
        align-items: center;
    }
}

h1,
label {
    color: black;
}

li {
    width: 100%;
    height: 50px;
    list-style: none;
    background-color: #ffffff;
    padding: 13px;
    cursor: pointer;
}

li:hover {
    background-color: #cacaca;
}

.todolist {
    min-height: 100vh;
    display: flex;
    align-items: center;
}

.todo_wrapper {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    background-color: #ededed;
    height: 500px;
    width: 500px;
    border-radius: 5px;
}

.add_block {
    display: inline-flex;
    width: 85%;
    justify-content: space-around
}

.todo_input {
    border: none;
    outline-style: none;
    padding: .375rem .75rem;
    width: 85%;
}

.add_item {
    border: none;
    background-color: #808080;
    margin-left: 10px;
    color: aliceblue;
    cursor: pointer;
    padding: .375rem .75rem;
}

.nav_block {
    margin-top: 15px;
    border-bottom: 1px solid #808080;
    width: 100%
}

.nav_item {
    border: none;
    background-color: #ffffff;
    margin-left: 10px;
    color: #808080;
    cursor: pointer;
    padding: .375rem .75rem
}

.todo_item {
    width: 85%;
    height: 300px;
    overflow-y: auto;
    margin-top: 20px;
    scrollbar-width: thin;
    padding: inherit;
}

.btn_icon {
    background: none;
    border: none;
    cursor: pointer;
}

.line-through {
    text-decoration: line-through
}
</style>