<!-- State of component represented by these 3 top-level variables -->
<script>
  // svelte uses 'export' keyword to mark a variable declaration as a property or prop,
  // which means it is accessible to consumers of the component.
  export let todos = [];
  // '$:' is used to tell Svelte that these variables to be reactive.. which means
  //rerun this code whenever any of the referenced values change
  $: totalTodos = todos.length;
  $: completedTodos = todos.filter((todo) => todo.completed).length;

  let newTodoName = "";
  let newTodoId;
  $: {
    if (totalTodos === 0) {
      newTodoId = 1;
    } else {
      newTodoId = Math.max(...todos.map((t) => t.id)) + 1;
    }
  }

  function addTodo() {
    todos = [...todos, { id: newTodoId, name: newTodoName, completed: false }];
    newTodoName = "";
  }

  function removeTodo(todo) {
    todos = todos.filter((t) => t.id !== todo.id);
  }

  let filter = "all";
  const filterTodos = (filter, todos) =>
    filter === "active"
      ? todos.filter((t) => !t.completed)
      : filter === "completed"
      ? todos.filter((t) => t.completed)
      : todos;
</script>

<!-- Todos.svelte -->
<div class="todoapp stack-large">
  <!-- NewTodo -->
  <form on:submit|preventDefault={addTodo}>
    <h2 class="label-wrapper">
      <label for="todo-0" class="label__lg"> What needs to be done? </label>
    </h2>
    <input
      bind:value={newTodoName}
      type="text"
      id="todo-0"
      autocomplete="off"
      class="input input__lg"
    />

    <button type="submit" disabled="" class="btn btn__primary btn__lg">
      Add
    </button>
  </form>

  <!-- Filter -->
  <div class="filters btn-group stack-exception">
    <button
      class="btn toggle-btn"
      class:btn__primary={filter === "all"}
      aria-pressed={filter === "all"}
      on:click={() => (filter = "all")}
    >
      <span class="visually-hidden">Show</span>
      <span>All</span>
      <span class="visually-hidden">tasks</span>
    </button>
    <button
      class="btn toggle-btn"
      class:btn__primary={filter === "active"}
      aria-pressed={filter === "active"}
      on:click={() => (filter = "active")}
    >
      <span class="visually-hidden">Show</span>
      <span>Active</span>
      <span class="visually-hidden">tasks</span>
    </button>
    <button
      class="btn toggle-btn"
      class:btn__primary={filter === "completed"}
      aria-pressed={filter === "completed"}
      on:click={() => (filter = "completed")}
    >
      <span class="visually-hidden">Show</span>
      <span>Completed</span>
      <span class="visually-hidden">tasks</span>
    </button>
  </div>

  <!-- TodosStatus -->
  <h2 id="list-heading">
    {completedTodos} out of {totalTodos} items completed
  </h2>

  <!-- Todos -->
  <!-- To-dos -->
  <ul role="list" class="todo-list stack-large" aria-labelledby="list-heading">
    {#each filterTodos(filter, todos) as todo (todo.id)}
      <li class="todo">
        <div class="stack-small">
          <div class="c-cb">
            <input
              type="checkbox"
              id="todo-{todo.id}"
              on:click={() => (todo.completed = !todo.completed)}
              checked={todo.completed}
            />
            <label for="todo-{todo.id}" class="todo-label"> {todo.name} </label>
          </div>
          <div class="btn-group">
            <button type="button" class="btn">
              Edit <span class="visually-hidden">{todo.name}</span>
            </button>
            <button
              type="button"
              class="btn btn__danger"
              on:click={() => removeTodo(todo)}
            >
              Delete <span class="visually-hidden">{todo.name}</span>
            </button>
          </div>
        </div>
      </li>
    {:else}
      <li>Nothing to do here!</li>
    {/each}
  </ul>

  <hr />

  <!-- MoreActions -->
  <div class="btn-group">
    <button type="button" class="btn btn__primary">Check all</button>
    <button type="button" class="btn btn__primary">Remove completed</button>
  </div>
</div>
