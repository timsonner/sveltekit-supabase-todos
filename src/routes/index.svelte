<script>
    import { onMount } from "svelte";
    import { supabase } from "$lib/supabaseClient";
    import RowView from "$lib/components/RowView.svelte";

    let todos = [];
    let loading = true;
    let newTask = "";

    onMount(async () => {
        fetchTasks();
    });

    const fetchTasks = async () => {
        loading = true;
        let { data, error } = await supabase.from("todos").select("*");
        if (error) {
            alert(error.message);
        }
        todos = data;
        // Sort the todos array alphabetically
        todos.sort((a, b) => a.task.localeCompare(b.task));
        loading = false;
    };

    const updateTask = async (todo) => {
        console.table(todo);
        // .update updates the 'task' column with the current todo.task
        // .eq grabs the correct db entry by matching the task.id to a db entry with the same id in the 'id' column
        try {
            const { data, error } = await supabase
                .from("todos")
                .update({ task: todo.task })
                .eq("id", todo.id);
        } catch (error) {
            alert(error.message);
        }
        await fetchTasks();
    };

    const updateCompleted = async (todo) => {
        console.log("updating completed from index.svelte");
        try {
            const { data, error } = await supabase
                .from("todos")
                .update({ completed: todo.completed })
                .eq("id", todo.id);
        } catch (error) {
            alert(error.message);
        }
        await fetchTasks();
    };

    const removeTask = async (todo) => {
        try {
            // delete db entry from column "id" matching todo.id
            const { data, error } = await supabase
                .from("todos")
                .delete()
                .eq("id", todo.id);
        } catch (error) {
            alert(error.message);
        }
        await fetchTasks();
    };

    const insertTask = async () => {
        // check input contains something
        if (newTask == "") {
            alert("New task cannot be empty");
            return false;
        }
        // check if database already contains a task, reset newTask to avoid continous alert
        if (supabase.from("todos").select("*").eq("task", newTask)) {
            alert(`Task ${newTask} already exists`);
            newTask = "";
            return false;
        }
        try {
            const { data, error } = await supabase
                .from("todos")
                .insert([{ task: newTask }]);
        } catch {
            alert(error.message);
        }
        newTask = "";
        await fetchTasks();
    };

    const handleEnter = (event) => {
        // Accept "Enter" as alternative to clicking Add button
        if (event.key === "Enter") {
            insertTask();
        }
    };
</script>

<svelte:window on:keypress={handleEnter} />

<div class="addTask">
    <input
        type="text"
        bind:value={newTask}
        placeholder="Enter a new task here..."
        on:key={handleEnter}
    />
    <button on:click={insertTask}>Add Task</button>
</div>

{#if !loading}
    {#each todos as todo}
        <!-- pass props to the RowView component -->
        <!-- props can be reactive variables, functions, or objects -->
        <RowView
            {todo}
            {updateTask}
            {updateCompleted}
            {removeTask}
            {insertTask}
        />
    {:else}
        <p>No todos found</p>
    {/each}
{:else}
    Querying database...  
    
    <div class="loader" />
{/if}

<style>
    .addTask {
        display: flex;
        margin-bottom: 0.5em;
    }

    .loader {
        border: 16px solid darkgrey;
        border-top: 16px solid lightgrey;
        border-radius: 50%;
        width: 40px;
        height: 40px;
        animation: spin 1s linear infinite;
    }

    @keyframes spin {
        0% {
            transform: rotate(0deg);
        }
        100% {
            transform: rotate(360deg);
        }
    }
</style>
