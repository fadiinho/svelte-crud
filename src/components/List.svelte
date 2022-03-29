<script>
  import { quintOut } from 'svelte/easing';
  import { crossfade, slide } from 'svelte/transition';
  import { flip } from 'svelte/animate';

  export let listItems;
  export let remove;

  const [send, receive] = crossfade({
    duration: (d) => Math.sqrt(d * 200),

    fallback(node) {
      const style = getComputedStyle(node);
      const transform = style.transform === 'none' ? '' : style.transform;

      return {
        duration: 400,
        easing: quintOut,
        css: (t) => `
          transform: ${transform} scale(${t});
          opacity: ${t}
        `
      };
    }
  });
</script>

<div transition:slide={{ delay: 100, duration: 300, easing: quintOut }} class="list">
  {#each listItems as item (item.id)}
    <div
      in:receive={{ key: item.id }}
      out:send={{ key: item.id }}
      animate:flip={{ duration: 100 }}
      class="item"
    >
      <div class="container">
        <h3>{item.title}</h3>
        <div>{item.content}</div>
      </div>
      <span on:click={() => remove(item.id)} />
    </div>
  {/each}
</div>

<style>
  .list {
    display: flex;
    flex-direction: column;
    padding: 10px;
    border: 2px solid var(--secondary-color);
    border-radius: 8px;
    width: 100%;
    background-color: var(--primary-color);
  }

  .item {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    border: 1px solid #ffffff55;
    border-radius: 4px;
    margin: 4px;
    padding: 10px;
    text-align: left;
    box-shadow: 1px 1px 1px #ffffffaa;
  }

  .container > * {
    align-self: flex-start;
  }

  .container h3 {
    color: #ffffffff;
    margin-bottom: 4px;
  }

  .container div {
    color: #ffffffea;
  }

  .item span {
    position: relative;
    display: flex;
    justify-content: center;
    width: 15px;
    height: 15px;
    cursor: pointer;
    flex-shrink: 0;
  }

  .item span::before {
    content: '';
    position: absolute;
    width: 2px;
    height: 15px;
    background-color: var(--secondary-color);
    transform: rotate(-45deg);
  }

  .item span::after {
    content: '';
    position: absolute;
    width: 2px;
    height: 15px;
    background-color: var(--secondary-color);
    transform: rotate(45deg);
  }
</style>
