<script>
    import { onMount } from 'svelte';
    import { browser } from '$app/environment';
  
    let todoText = '';
    let todos = [];
    let selectedTodoIndex = -1;
    let isNoteWindowOpen = false;
  
    onMount(() => {
      if (browser) {
        const storedTodos = JSON.parse(localStorage.getItem('todos'));
        if (storedTodos) {
          todos = storedTodos;
        }
      }
    });
  
    function saveTodos() {
      if (browser) {
        localStorage.setItem('todos', JSON.stringify(todos));
      }
    }
  
    function addTodo() {
      todos.push({ text: todoText, done: false, note: '' });
      todos = todos;
      todoText = '';
      saveTodos();
    }
  
    function remove(index) {
      todos.splice(index, 1);
      todos = todos;
      saveTodos();
    }
  
    function openNoteWindow(index) {
      selectedTodoIndex = index;
      isNoteWindowOpen = true;
    }
  
    function closeNoteWindow() {
      selectedTodoIndex = -1;
      isNoteWindowOpen = false;
    }
  </script>
  
  <h1>TODO APP</h1>
  
  <div class="container">
    <div class="todo-form">
      <input type="text" class="todo-input" bind:value={todoText} placeholder="Add a new todo" />
      <button on:click={addTodo}>ADD</button>
    </div>
  
    <div class="todo-list">
      {#each todos as todo, index}
        <div class="todo-entry" class:done={todo.done}>
          <div class="todo-content">
            <input type="checkbox" bind:checked={todo.done} />
            <div class="todo-text" on:click={() => openNoteWindow(index)}>{todo.text}</div>
          </div>
          <button class="delete" on:click={() => remove(index)}>X</button>
        </div>
  
        {#if selectedTodoIndex === index}
          <div class="note-window">
            <textarea bind:value={todos[selectedTodoIndex].note} placeholder="Add a note"></textarea>
            <button on:click={closeNoteWindow}>Close</button>
          </div>
        {/if}
      {/each}
    </div>
  </div>
  
  <style>
    .container {
      max-width: 400px;
      margin: 0 auto;
      padding: 20px;
      box-sizing: border-box;
    }
  
    .todo-form {
      display: flex;
      margin-bottom: 10px;
    }
  
    .todo-input {
      flex-grow: 1;
      padding: 8px;
      border: none;
      border-radius: 4px;
      font-size: 16px;
    }
  
    .todo-input::placeholder {
      color: #999;
    }
  
    .todo-input:focus {
      outline: none;
    }
  
    .todo-form button {
      padding: 8px 16px;
      background-color: #4caf50;
      border: none;
      border-radius: 4px;
      color: #fff;
      font-size: 16px;
      cursor: pointer;
    }
  
    .todo-list {
      border: 1px solid #ccc;
      border-radius: 4px;
    }
  
    .todo-entry {
      display: flex;
      align-items: center;
      padding: 10px;
      border-bottom: 1px solid #ccc;
      cursor: pointer;
  }
  
  .todo-entry:last-child {
  border-bottom: none;
  }
  
  .done {
  color: #999;
  text-decoration: line-through;
  }
  
  .todo-content {
  display: flex;
  align-items: center;
  flex-grow: 1;
  }
  
  .todo-text {
  margin-left: 10px;
  }
  
  .delete {
  background-color: transparent;
  border: none;
  color: #999;
  font-size: 16px;
  cursor: pointer;
  }
  
  .delete:hover {
  color: #f44336;
  font-weight: bold;
  }
  
  .note-window {
  margin-top: 10px;
  padding: 10px;
  background-color: #f0f0f0;
  border-radius: 4px;
  }
  
  .note-window textarea {
  width: 100%;
  height: 80px;
  padding: 8px;
  border: none;
  border-radius: 4px;
  resize: vertical;
  }
  
  .note-window button {
  padding: 8px 16px;
  background-color: #ccc;
  border: none;
  border-radius: 4px;
  color: #fff;
  font-size: 16px;
  cursor: pointer;
  }
  </style>