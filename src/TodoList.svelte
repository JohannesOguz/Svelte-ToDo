<script>
    import { onMount } from 'svelte';
  
    let todos = [];
    let newTodo = '';
  
    onMount(async () => {
      const res = await fetch('http://localhost:3001/todos');
      todos = await res.json();
    });
  
    async function addTodo() {
      if (!newTodo.trim()) return;
  
      const res = await fetch('http://localhost:3001/todos', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ text: newTodo, done: false })
      });
  
      const added = await res.json();
      todos = [...todos, added];
      newTodo = '';
    }
  
    async function toggleTodo(todo) {
      todo.done = !todo.done;
  
      await fetch(`http://localhost:3001/todos/${todo.id}`, {
        method: 'PUT',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(todo)
      });
    }
  
    async function deleteTodo(todo) {
      await fetch(`http://localhost:3001/todos/${todo.id}`, {
        method: 'DELETE'
      });
  
      todos = todos.filter(t => t.id !== todo.id);
    }
  </script>
  
  <h2>Todo-lista</h2>
  
  <input bind:value={newTodo} placeholder="Ny todo" />
  <button on:click={addTodo}>Lägg till</button>
  
  <ul>
    {#each todos as todo (todo.id)}
      <li>
        <input type="checkbox" bind:checked={todo.done} on:change={() => toggleTodo(todo)} />
        <span style="text-decoration: {todo.done ? 'line-through' : 'none'}">
          {todo.text}
        </span>
        <button on:click={() => deleteTodo(todo)}>Ta bort</button>
      </li>
    {/each}
  </ul>
  