<template>
<div class="todo_list">
        <transition-group name="list">
            <todo-item
                draggable="true"
                @dragstart="handleDragStart"
                @dragover="handleDragOver"
                @drop="handleDrop"
                v-for="todo in todos"
                :key="todo.id"
                :todo="todo"
                @removeTodo="$emit('removeTodo', todo)"
                @changeComplete="$emit('changeComplete', todo)"
            ></todo-item>
        </transition-group>
</div>
</template>

<script>
import TodoItem from '@/components/TodoItem.vue'

export default {
    components: {
        TodoItem
    },
    name: 'todo-list',
    props: {
        todos: {
            type: Array,
            required: true
        }
    },
    data() {
        return {
            dragSrcEl: null
        }
    },
    methods: {
        handleDragStart(e) {
            e.target.opacity = '0.4';
            this.dragSrcEl = e.target;
            e.dataTransfer.effectAllowed = 'move';
        },

        handleDragOver(e) {
            if(e.preventDefault) {
                e.preventDefault();
            }
            e.dataTransfer.dropEffect = 'move';
        },
        
        handleDrop(e) {
            if(e.stopPropagation) {
                e.stopPropagation();
            }

            if(this.dragSrcEl !== e.target.parentElement) {
                this.$emit('swapTodos', parseInt(this.dragSrcEl.getAttribute('data-id'), 10),
                    parseInt(e.target.parentElement.getAttribute('data-id'), 10));
            }
        }
    },
    emits: ["removeTodo", "changeComplete", "swapTodos"]
}
</script>

<style scoped>
    .todo_list {
        width: 100%;
        padding: 0 1rem;
        flex: 1;

    }
</style>