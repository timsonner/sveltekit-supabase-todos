<script>
    import { onMount } from "svelte";
    import { supabase } from "$lib/supabaseClient";
    import RowView from "$lib/components/RowView.svelte";

    let todos = [];
    let loading = true;

    onMount(async () => {
        fetchTasks()
    });

    const fetchTasks = async () => {
        let { data, error } = await supabase.from("todos").select("*");
        if (error) {
            alert(error);
        }
        todos = data;
        console.table(todos);
        loading = false;
    }

    const updateTask = async (todo) => {
        console.table(todo);
        // .update updates the 'task' column with the current todo.task
        // .eq grabs the correct db entry by matching the task.id to a db entry with the same id in the 'id' column
        try {
            const { data, error } = await supabase
                .from('todos')
                .update({ task: todo.task })
                .eq('id', todo.id);
        } catch (error) {
            alert(error.value);
        }
    }

    const updateCompleted = async (todo) => {
        console.log('updating completed from index.svelte')
        try {
            const { data, error } = await supabase
                .from('todos')
                .update({ completed: todo.completed })
                .eq('id', todo.id)
        } catch (error) {
            alert(error.message)
        }
    }
</script>

{#if !loading}
    {#each todos as todo}
        <!-- pass props to the RowView component -->
        <!-- props can be reactive variables, functions, or objects -->
        <RowView {todo} {updateTask} {updateCompleted}/>
    {:else}
        <p>No todos found</p>
    {/each}
{:else}
    <p>Loading todos...</p>
{/if}
