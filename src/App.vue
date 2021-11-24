<template>
    <div class="main_form__content">
        <nav class="navbar-container">
            <ul class="nav navbar">
                <li class="nav-item"><a href="#" @click="this.filterOption = 'all'"
                    :class="{ active: this.filterOption === 'all' }"
                >All</a></li>
                <li class="nav-item"><a href="#" @click="this.filterOption = 'active'"
                    :class="{ active: this.filterOption === 'active' }"
                >Active</a></li>
                <li class="nav-item"><a href="#" @click="this.filterOption = 'completed'"
                    :class="{ active: this.filterOption === 'completed' }"
                >Completed</a></li>
            </ul>
        </nav>
        <div class="main_form__head">
            <input type="text" class="input input_add" v-model="newTodo">
            <button class="btn btn_add" @click="addTodo">Add</button>
        </div>
                    
                
                    <todo-list
                        :todos="todos" 
                        @removeTodo="removeTodo"
                        @changeComplete="changeComplete"
                        @swapTodos="swapTodos"
                    ></todo-list>

        <button class="btn btn_clear" @click="clear">Clear all</button>
    </div>
</template>

<script>
import TodoList from '@/components/TodoList.vue'
import axios from 'axios'
export default {
    components: {
        TodoList
    },

    data() {
        return {
            newTodo: '',
            todos: [
            ],
            filterOption: 'all'
        }
    },

    methods: {
        addTodo() {
            if(this.newTodo !== '') {
                const newTodoItem = {content: this.newTodo, complited: false, order: -1};
                axios.post('https://localhost:44364/Todo', newTodoItem);
                this.newTodo = '';
            }
        },

        removeTodo(todo) {
            this.todos = this.todos.filter(t => t.id !== todo.id);
            axios.delete(`https://localhost:44364/Todo/${todo.id}`);
        },

        changeComplete(todo)
        {
            console.log("changed");
            todo.complited = !todo.complited;
            axios.put(`https://localhost:44364/Todo/${todo.id}`, todo);
        },

        clear() {
            this.todos.forEach(todo => {
                axios.delete(`https://localhost:44364/Todo/${todo.id}`);
            });
        },

        swapTodos(firstId, secondId) {
            let firstTodoIndex = 0;
            let secondTodoIndex = 0;
            for(let i = 0; i < this.todos.length; ++i) {
                if(this.todos[i].id === firstId) {
                    firstTodoIndex = i;
                }
                if(this.todos[i].id === secondId) {
                    secondTodoIndex = i;
                }
            }
            const temp = this.todos[firstTodoIndex];
            this.todos[firstTodoIndex] = this.todos[secondTodoIndex];
            this.todos[secondTodoIndex] = temp;
            this.todos[firstTodoIndex].order = this.todos[secondTodoIndex].order;
            this.todos[secondTodoIndex].order = temp.order;
            axios.put(`https://localhost:44364/Todo/${firstId}/${secondId}`);
        },

        getTodos() {
            axios.get('https://localhost:44364/Todo/' + this.filterOption).then(response => {
                const sorted = response.data.sort((a, b) => {
                    return a.order - b.order;
                });
                if(this.todos.length !== sorted.length) {
                    this.todos = sorted;
                }
                else {
                    for(let i = 0; i < this.todos.length; ++i) {
                        const resp_todo = sorted.filter(t => t.id === this.todos[i].id);
                        if(JSON.stringify(this.todos[i]) !== JSON.stringify(sorted[0])) {
                            this.todos = sorted;
                            break;
                        }
                    }
                }
            });
        }
    },

    mounted() {
        setInterval(() => {
            this.getTodos();
        }, 1000);
    }
}
</script>

<style>
    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
    }

    html {
        height: 100%;
        background: linear-gradient(210deg, #B4B0BE, rgb(0, 65, 106));
    }

    body {
        overflow: hidden;
        height: 100%;
        display: flex;
        align-items: center;
        justify-content: center;
    }

    button {
        background: none;
        border: none;
    }

    button:focus {
        outline: none;
    }

    #app {
        height: 80%;
        width: 30%;
    }
    .main_form__content {
        height: 100%;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: space-between;
        border-radius: 23px;
        background: white;
        overflow-y: scroll;
        box-shadow: 0 0 5px rgba(25, 25, 25, 0.25);
    }

    .main_form__content::-webkit-scrollbar {
        display: none;
    }

    .btn {
        font-size: 20px;
        margin: 0 0.5em;
        border-radius: 2em;
        padding: 0.75em 1.5em;
        cursor: pointer;
        background: none;
        color: rgb(0, 65, 106);
        border: 1px solid;
        transition: 250ms ease-out;
    }

    .btn:hover, .btn:focus {
        color: white;
        background: #FC6767;
        box-shadow: 0 0 15px 5px #FC6767;
        border: none !important;
    }

    .main_form__head {
        width: 100%;
        padding: 1.5rem 1rem 0 1rem;
        display: flex;
        margin-bottom: 15px;
    }

    .input_add {
        width: 100%;
        font-size: 20px;
        margin: 0 0.5em;
        border-radius: 2em;
        padding: 0.75em 1.5em;
        background: none;
        border: #e3e3e3 1px solid;
        transition: border 250ms ease-out;
    }

    .input_add:focus {
        border: rgb(0, 65, 106) 1px solid;
        outline: none;
    }

    .btn_clear {
        margin-bottom: 9px;
        margin-top: 9px;
    }

    a {
        color: #A400FF;
        text-decoration: none;
    }

    .navbar-container {
        width: 100%;
        height: 24px;
        margin-bottom: 15px;
    }

    .nav, .navbar {
        width: 100%;
        margin: auto;
        font-size: 20px;
        list-style: none;
        display: flex;
        justify-content: space-between;
        flex-wrap: nowrap;
        margin-top: 15px;
        padding: 0 30px;
    }

    .active {
        border-bottom: 3px solid #A400FF;
    }
    
</style>