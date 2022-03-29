<script lang="ts">
  import List from '../components/List.svelte';
  import { slide } from 'svelte/transition';
  import { quintOut } from 'svelte/easing';

  const generateId = (length = 16) => {
    return parseInt(
      Math.ceil(Math.random() * Date.now())
        .toPrecision(length)
        .toString()
        .replace('.', '')
    );
  };

  let title: string;
  let content: string;

  let listItems = [];

  function messageError(err: number) {
    let text: HTMLElement;

    if (err === 0) {
      text = document.querySelector('input');
    } else if (err == 1) {
      text = document.querySelector('textarea');
    } else {
      messageError(0);
      messageError(1);
      return;
    }

    function toggle() {
      text.classList.toggle('error');
    }

    if (text.classList.contains('error')) return;

    toggle();
    setTimeout(toggle, 1000);
  }

  function add() {
    if (!title && !content) {
      messageError(2);
      return;
    } else if (!title) {
      messageError(0);
      return;
    } else if (!content) {
      messageError(1);
      return;
    }

    listItems = [...listItems, { id: generateId(), title, content }];
    //title = '';
    //content = '';
  }

  function remove(id: number) {
    listItems = [...listItems.filter((item) => item.id !== id)];
  }

  function handleKey(e: KeyboardEvent) {
    if (e.key === 'Enter' && e.shiftKey) {
      e.preventDefault();
      add();
    }
  }
</script>

<h1>Crud</h1>
<div transition:slide={{ delay: 250, duration: 300, easing: quintOut }} class="container">
  <input on:keypress={handleKey} bind:value={title} name="title" type="text" placeholder="Title" />
  <textarea on:keypress={handleKey} bind:value={content} name="content" placeholder="Content" />
  <button on:click={add}>Add</button>
  {#if listItems.length >= 1}
    <List {listItems} {remove} />
  {/if}
</div>

<style>
  :global(*) {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }

  :root {
    --bg-color: #000;
    --primary-color: #040f16;
    --secondary-color: #0094c6;
    --error-color: #f00;
  }

  :global(body) {
    display: flex;
    justify-content: center;
    min-height: 100vh;
    text-align: center;
    background-color: var(--bg-color);
    color: var(--secondary-color);
  }

  .container {
    display: flex;
    height: fit-content;
    align-items: center;
    flex-direction: column;
    padding: 4px;
    transition: height 2s linear;
  }

  input {
    width: 100%;
    max-width: 300px;
    outline: none;
    border: 2px solid var(--secondary-color);
    margin: 2px;
    padding: 2px;
    border-radius: 5px;
    background-color: var(--primary-color);
    color: #fff;
    max-width: 300px;
  }

  textarea {
    outline: none;
    border: 2px solid var(--secondary-color);
    min-width: 300px;
    min-height: 200px;
    max-width: calc(100vw - 50px);
    max-height: calc(100vh - 150px);
    margin: 2px;
    padding: 2px;
    border-radius: 5px;
    background-color: var(--primary-color);
    color: #fff;
  }

  input,
  textarea {
    transition: border-color 0.1s;
  }

  textarea::placeholder {
    color: #ffffffaa;
  }

  input::placeholder {
    color: #ffffffaa;
  }

  button {
    border: 2px solid var(--secondary-color);
    background-color: var(--primary-color);
    color: #fff;
    border-radius: 5px;
    margin: 2px;
    width: 100px;
    cursor: pointer;
  }

  button:active {
    background-color: #ffffff55;
  }

  input:global(.error),
  textarea:global(.error) {
    border: 2px solid var(--error-color);
    animation: error-animation 0.1s linear;
  }

  @keyframes error-animation {
    from {
      transform: translateX(10px);
    }
    to {
      transform: translateX(-10px);
    }
  }
</style>
