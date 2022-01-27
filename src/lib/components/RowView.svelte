<script>
    export let todo, updateTask, updateCompleted
</script>

<div class="todo">
    <input 
        type="checkbox" 
        bind:checked={todo.completed}
        on:change={
                updateCompleted(todo)}/>
    
    <!-- on input, call updateTodo() -->
    <!-- event.currentTarget is this input -->
    <!-- updates the task property of todo -->
    <input 
        type="text" 
        bind:value={todo.task}
        on:input={updateTask(todo)}>

    <button>Edit</button>
    <button>Delete</button>
</div>
<style>
    .todo {
        display: flex;
        margin-bottom: 0.25em;
    }
</style>