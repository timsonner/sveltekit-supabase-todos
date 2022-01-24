<script>
    import { onMount } from "svelte";
    import { supabase } from "$lib/supabaseClient";
    import RowView from "$lib/components/RowView.svelte";

    let todos = [];
    let loading = true;

    onMount(async () => {
        let { data, error } = await supabase.from("todos").select("*");
        if (error) {
            alert(error);
        }
        todos = data;
        console.table(todos);
        loading = false;
    });

const updateTodo = async (todo) => {
    console.table(todo)
    // .update updates the 'task' column with the current todo.task
    // .eq grabs the correct db entry by matching the current task.id to an entry with the same id in the 'id' column
    try {
        const { data, error } = await supabase
        .from('todos')
        .update({ task: todo.task })
        .eq('id', todo.id)

    } catch (error) {
        alert(error.value)
    }
}

</script>

{#if !loading}
    {#each todos as todo}
        <!-- pass props to the RowView component -->
        <RowView {todo} {updateTodo}/>
    {:else}
        <p>No todos found</p>
    {/each}
{:else}
    <p>Loading todos...</p>
{/if}
