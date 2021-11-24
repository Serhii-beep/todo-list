<template>
<div :data-id="this.todo.id">
    <li class="todo_item" :class="{complete_li: todo.complited}" @click="$emit('changeComplete', todo)">
        <div class="content" :class="{complete_content: todo.complited}">
            {{ todo.content }}
        </div>
        <button class="btn btn_remove" @click="remove" :class="{complete_button: todo.complited}">x</button>
    </li>
</div>
</template>

<script>
export default {
    name: 'todo-item',
    props: {
        todo: {
            type: Object,
            required: true
        }
    },

    methods: {
        remove(event) {
            event.stopPropagation();
            this.$emit('removeTodo', this.todo);
        }
    },
    emits: ["removeTodo", "changeComplete"]
}
</script>

<style scoped>
.todo_item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0.5em;
    margin-bottom: 0.5em;
    border-radius: 3px;
    transition: 300ms;
    color: rgb(0, 65, 106);
    border: 1px solid rgb(0, 65, 106);
    border-radius: 2em;
}

.todo_item:hover {
    background: rgb(215, 219, 186);
    cursor: pointer;
}

.complete_button, .complete_content {
    color: #B10361;
}

.complete_content {
    text-decoration: line-through;
}

.complete_li {
    border-color: #B10361;
}

.btn_remove {
    margin-left: 0.5em;
    background: none;
    border: 1px solid;
    padding: 0;
    line-height: 1;
    width: 3em;
    height: 3em;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 50%;
    font-size: 80%;
    transition: 300ms;
}

.content:hover {
    cursor: pointer;
}

.content {
    font-size: 20px;
    transition: 300ms;
    max-width: 300px;
    overflow-wrap: break-word;
    word-wrap: break-word;
    hyphens: auto;
}


.list-enter-active,
    .list-leave-active {
        transition: all 0.4s ease;
    }

    .list-enter-from,
    .list-leave-to {
        opacity: 0;
        transform: translateX(30px);
    }

    .list-move {
        transition: transform 0.4s ease;
    }
</style>