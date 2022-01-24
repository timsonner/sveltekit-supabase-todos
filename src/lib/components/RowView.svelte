<script>
    export let todo
</script>

<div>
    <input type="checkbox" checked={todo.completed}>
    <input type="text" value={todo.task}>
    <button>❌</button>
    <button>📝</button>
</div>