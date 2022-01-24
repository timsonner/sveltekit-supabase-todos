<script>
import { onMount } from "svelte";
import { supabase } from '$lib/supabaseClient'
import { each } from "svelte/internal";
import RowView from '$lib/components/RowView.svelte'

let todos = []

    onMount(async () => {
let {data, error} = await supabase.from('todos').select('*')
if (error) {
    alert(error)
}
todos = data
console.table(todos)
    })
</script>

{#each todos as todo}
    
<RowView {todo} />
{/each}
