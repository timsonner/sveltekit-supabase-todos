<script>
    export let todo, updateTask, updateCompleted, removeTask
</script>

<div class="task" class:completed={todo.completed}>
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
    <button on:click={removeTask(todo)}>Delete</button>
</div>

<style>
    .task {
        display: flex;
        margin-bottom: 0.25em;
    }

    .completed {
        opacity: 0.5;
    }

    .completed [type="text"] {
        text-decoration: line-through;
        text-decoration-color: red;
        text-decoration-thickness: 15%;
    }
    
</style>