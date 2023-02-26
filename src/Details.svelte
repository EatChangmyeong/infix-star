<script lang="ts">
  import { onMount } from 'svelte';
  import { selected as selectedStore } from './Table.svelte';
  import DocLink from './DocLink.svelte';
  import closeImg from './close.svg';
  import type { Entry } from './table.ts';
  export let selected: [number, number, Entry];
  
  let
    fixity: string,
    assoc: number,
    prec: number,
    entry: Entry,
    parenthesize: boolean,
    visible: string[];
  $: [assoc, prec, entry] = selected;
  $: fixity = `infix${['l', '', 'r'][selected[0]]} ${selected[1]}`;
  $: parenthesize = !(
    'A' <= entry.name[0] && entry.name[0] <= 'Z' ||
    'a' <= entry.name[0] && entry.name[0] <= 'z' ||
    entry.name[0] == '_'
  );
  $: visible = entry.visible.filter(x => x !== '_');

  onMount(() => {
     document.body.classList.add('margin');
    return () => document.body.classList.remove('margin');
  });
</script>

<input type="image" id="close" alt="close" src={closeImg} on:click|preventDefault={() => $selectedStore = undefined}>
<div id="details">
  <p>
    <code class="small">{fixity}</code>
    <br>
    {#if entry.context}
      <code>{entry.context}</code>
      <br>
    {/if}
    <code>{entry.decltype ? entry.decltype + ' ' : ''}<span class="black">{parenthesize ? `(${entry.name})` : entry.name}</span> :: {entry.type}</code>
  </p>
  <p>
    <span class="small">Defined in</span><br>
    <DocLink {entry} mod={entry.defined} src />
  </p>
  {#if visible.length}
  <p>
    <span class="small">Visible in</span>
    {#each visible as mod}
      <br>
      <DocLink {entry} {mod} />
    {/each}
  </p>
  {/if}
  <p class="small sans">Links are auto-generated and have chances to be broken.</p>
</div>

<style>
  #details {
    position: fixed;
    left: 0.5rem;
    bottom: 0.5rem;
    border-radius: 0.5rem;
    padding: 0.5rem;
    width: calc(100% - 1rem);
    height: calc(100vh / 3 - 0.5rem);
    overflow: auto;
    white-space: nowrap;
    background-color: white;
    box-shadow: 0 0.25rem 0.5rem #00000080;
    z-index: 1;
  }
  #close {
    position: fixed;
    right: 1rem;
    bottom: calc(100vh / 3 + 0.5rem);
    width: 2rem;
    height: 2rem;
  }
  p {
    margin: 0 0 0.25rem;
  }
  p:last-child {
    margin: 0;
  }
  code {
    color: #777;
    background: none;
  }
  .black {
    color: #000;
  }
</style>