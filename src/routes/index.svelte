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
        if (newTask !== "") {
            try {
                const { data, error } = await supabase
                    .from("todos")
                    .insert([{ task: newTask }]);
            } catch {
                alert(error.message);
            }
            newTask = "";
            await fetchTasks();
        } else {
            alert("New task cannot be empty");
        }
    };
</script>

<div class="addTask">
    <input
        type="text"
        bind:value={newTask}
        placeholder="Enter a new task here..."
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
