<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js">
	import { onMount } from 'svelte';
	import { fade } from 'svelte/transition';
	import { fly } from 'svelte/transition';
	import FaTimesCircle from 'svelte-icons/fa/FaTimesCircle.svelte'

 	const ENTER_KEY = 13;
	const ESCAPE_KEY = 27;
	let quote = '';

	let currentFilter = 'all';
	let newTodo = '';
	let tempId = 4;
	let todos = [
		{id: 1, completed: false, title: 'Playing Fifa', edit: false},
		{id: 2, completed: false, title: 'Go to Store', edit: false},
		{id: 3, completed: false, title: 'Study Svelte', edit: false}
	];

    function addTodo(event){
		
		if(event.which === ENTER_KEY){
			todos = [...todos, {
				id: tempId, completed: false, title: newTodo, editing: false
			}];

			newTodo = '';
			tempId += 1; 
		}
	}

	function editTodo(todo){
		todo.edit = true;
		todos = todos;
	}

	function doneEdit(todo){
		todo.edit = false;
		todos = todos;
	}

	function doneEditKeydown(todo){
		if( event.which === ENTER_KEY ) {
			doneEdit(todo);
		}
	}

	function deleteTodo(id){
		todos = todos.filter(todo =>todo.id !== id);
	}

	function checkAllTodos(event){
		todos.forEach(todo => todo.completed = event.target.checked);
		todos = todos;
	}

	function clearCompleted(){
		todos = todos.filter(todo => !todo.completed);
	}

	function updateFilter(filter){
		currentFilter = filter;
	}

	// Fetch Quotes
	onMount(async () => {
		const res = await fetch('https://api.kanye.rest');
		const quotes = await res.json();
		return quote = quotes.quote;
	})

	 $: filteredTodos = currentFilter === 'all'
	 	? todos
		: currentFilter === 'completed'
			? todos.filter(todo => todo.completed)
			: todos.filter(todo => !todo.completed);
			
	$: todosReamaining = todos.filter(todo => !todo.completed).length;
</script>

<style>
*{
  box-sizing: border-box;
}
body {
  font-family: "Montserrat", sans-serif;
  font-weight: bold;
  background-color: #d9dfe4;
  color: #0648ab;
  font-size: 24px;
	height: auto;
}
body .todo {
  max-width: 500px;
  margin: 0 auto 20px;
}
body .todo .logo {
  width: 400px;
  margin: 20px auto;
}
body .todo .logo img {
  width: 100%;
}
body .todo .quote {
  max-width: 600px;
  margin: 30px auto;
}
body .todo .completed {
  text-decoration: line-through;
  color: grey;
}
body .todo .icon {
  color: #0648ab;
  width: 32px;
  height: 32px;
  opacity: 0.8;
  transition: all 0.3s ease-in-out;
  cursor: pointer;
}
body .todo .icon:hover {
  -webkit-transform: scale(1.2);
  -ms-transform: scale(1.2);
  transform: scale(1.2);
  opacity: 1;
}
</style>

<link href="https://fonts.googleapis.com/css?family=Gayathri|Montserrat&display=swap" rel="stylesheet">
<link href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">

<body>
	<div class="container">
	<div class="todo">
		<div class="logo">
			<img src="https://placeholder.com/wp-content/uploads/2018/10/placeholder.com-logo3.png" alt="" class="img-fluid">
		</div>
		<div class="quote">
			<div class="alert alert-info">
				<q>{quote}</q>
			</div>
		</div>		
		<div class="input-group input-group-lg mb-3">
			<div class="input-group-prepend">
			<span class="input-group-text">What Needs to be Done?</span>
			</div>
			<input
				type="text"
				class="form-control"
				bind:value={newTodo}
				on:keydown={addTodo}>
		</div>

	
		{#each filteredTodos as todo}
			<div class="d-flex justify-content-between mb-2">
				<div class="d-flex" transition:fly="{{ y:30, duration: 300 }}">
		
					<div class="input-group-text py-2">
						<input type="checkbox" bind:checked="{todo.completed}">
					</div>
					
					{#if todo.edit}

					<div class="input-group ml-2">
						<input 
							type="text"
							class="form-control"
							bind:value={todo.title}
							on:blur={doneEdit(todo)}
							on:keydown={doneEditKeydown(todo)}
						>
					</div>
					{:else}
						<div 
							class="ml-3"
							class:completed={todo.completed}
							on:dblclick={editTodo(todo)}>
							{todo.title}
						</div>
					{/if}			  
				</div>

				<div class="icon" on:click={deleteTodo(todo.id)}>
					<FaTimesCircle />
				</div>
			</div>
		{/each}

		<div class="alert alert-secondary d-flex justify-content-between p-3 mt-3">
			<div class="">
				<div class="">
					<div class="input-group">
						<div class="input-group-text">
							<input type="checkbox" on:change={checkAllTodos}>
						</div>
						
						<input type="text" disabled class="form-control" placeholder="Check All" style="width: 100px;">
					</div>
				</div>
			</div>
			<div>{todosReamaining} Todos Left</div>
		</div>	
		<div class="d-flex justify-content-between">
			<div class="btn-group">
				<button type="button" class="btn btn-primary" on:click={() => updateFilter('all')} class:active={currentFilter === 'all'}>All</button>
				<button type="button" class="btn btn-primary" on:click={() => updateFilter('active')} class:active={currentFilter === 'active'}>Active</button>
				<button type="button" class="btn btn-primary" on:click={() => updateFilter('completed')} class:active={currentFilter === 'completed'}>Completed</button>
			</div>
			<div>
				<button on:click={clearCompleted} class="btn btn-danger">Clear Completed</button>
			</div>
		</div>
	</div>	  
</div>
</body>
