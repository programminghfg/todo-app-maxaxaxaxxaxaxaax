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
      todos.push({ text: todoText, done: false, note: '', hasNote: false });
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
  
    function handleDragStart(event, index) {
      event.dataTransfer.effectAllowed = 'move';
      event.dataTransfer.setData('text/plain', index.toString());
    }
  
    function handleDragOver(event, index) {
      event.preventDefault();
      const draggedIndex = parseInt(event.dataTransfer.getData('text/plain'));
      if (draggedIndex !== index) {
        const newTodos = [...todos];
        const [draggedTodo] = newTodos.splice(draggedIndex, 1);
        newTodos.splice(index, 0, draggedTodo);
        todos = newTodos;
      }
    }
  
    function handleDrop(event) {
      event.preventDefault();
      saveTodos();
    }
  
    function addNote() {
      todos[selectedTodoIndex].hasNote = todos[selectedTodoIndex].note.trim() !== '';
      saveTodos();
      closeNoteWindow();
    }
  </script>
  
  <h1>Task Manager </h1>
  
  <div class="container">
    <div class="todo-form">
      <input type="text" class="todo-input" bind:value={todoText} placeholder="Add a new todo" />
      <button on:click={addTodo}>ADD</button>
    </div>
  
    <div class="todo-list">
      {#each todos as todo, index}
        <div
          class="todo-entry"
          class:done={todo.done}
          draggable="true"
          on:dragstart={(e) => handleDragStart(e, index)}
          on:dragover={(e) => handleDragOver(e, index)}
          on:drop={handleDrop}
        >
          <div class="todo-content">
            <input type="checkbox" bind:checked={todo.done} />
            <div class="todo-text" on:click={() => openNoteWindow(index)}>
              {todo.text}
              {#if todo.hasNote && todo.note.trim() !== ''}
                <span class="note-indicator">Note</span>
              {/if}
            </div>
          </div>
          <button class="delete" on:click={() => remove(index)}>X</button>
        </div>
  
        {#if selectedTodoIndex === index}
          <div class="note-window">
            <textarea bind:value={todos[selectedTodoIndex].note} placeholder="Add a note"></textarea>
            <button on:click={addNote}>Add Note</button>
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
  
    .note-indicator {
      margin-left: 5px;
      padding: 3px 6px;
      background-color: #f44336;
      color: white;
      font-size: 12px;
      border-radius: 4px;
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
  

    .note-window {
    margin-top: 10px;
    padding: 10px;
    background-color: #f0f0f0;
    border-radius: 4px;
    display: flex;
    flex-direction: column;
  }

  .note-window textarea {
    flex-grow: 1;
    width: 100%;
    height: 80px;
    padding: 8px;
    border: none;
    border-radius: 4px;
    resize: vertical;
  }

  .note-window button {
    margin-top: 10px;
    padding: 8px 16px;
    background-color: #ccc;
    border: none;
    border-radius: 4px;
    color: #fff;
    font-size: 16px;
    cursor: pointer;
  }  
    .todo-entry.dragging {
      opacity: 0.5;
    }
    
  </style>
  