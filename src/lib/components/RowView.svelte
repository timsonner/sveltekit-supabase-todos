<script>
    export let todo, updateTodo
</script>

<div class="todo">
    <input type="checkbox" checked={todo.completed}>
    
    <!-- on input, call updateTodo() -->
    <!-- event.currentTarget is this input -->
    <!-- updates the task property of todo -->
    <input 
        type="text" 
        value={todo.task}
        on:input={(event) => {
            todo.task = event.currentTarget.value
            updateTodo(todo)}}>

    <button>Edit</button>
    <button>Delete</button>
</div>
<style>
    .todo {
        display: flex;
        margin-bottom: 0.25em;
    }
</style>