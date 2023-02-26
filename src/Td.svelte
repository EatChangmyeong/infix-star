<script lang="ts">
  import type { Entry } from './table.ts';
  import { selected } from './Table.svelte';
  export let
    entries: Entry[] = [],
    associativity: number,
    precedence: number;
  
  let grouped: [string, Entry[]][] = [];
  $: grouped = groupByDef(entries).sort(([l], [r]) => moduleNameCmp(l, r));

  function moduleNameCmp(l: string, r: string): number {
    const ls = l.split('.'), rs = r.split('.');
    for(let i = 0;; i++)
      if(ls.length == i || rs.length == i)
        return ls.length - rs.length;
      else if(ls[i] !== rs[i])
        return (ls[i] > rs[i]) - (ls[i] < rs[i]);
  }
  function groupByDef(entries: Entry[]): [string, Entry[]][] {
    const map = new Map<Entry[]>();
    for(const entry of entries) {
      const def = entry.defined;
      if(!map.has(def))
        map.set(def, []);
      map.get(def)!.push(entry);
    }
    return [...map];
  }
</script>

<td>
  {#if $$slots.default}
    <p class="label">
      <slot />
    </p>
  {/if}
  {#each grouped as [mod, list]}
    <p>
      <span class="mod">{@html mod.replace(/\./g, '.<wbr>')}</span>
      <br>
      {#each list as entry, i}
        {#if i}
          &nbsp;
        {/if}
        <code>
          <button on:click={() => $selected = [associativity, precedence, entry]}>
            {entry.name}
          </button>
        </code>
      {/each}
    </p>
  {/each}
</td>

<style>
  td {
    border: 1px solid #808080;
    padding: 0.25rem;
    min-width: 15rem;
  }
  p {
    margin: 0.25rem 0;
  }
  button {
    all: inherit;
    cursor: pointer;
  }
  .mod {
    font-style: italic;
    opacity: 0.6;
  }
  .label {
    display: inline-block;
    border: 1px solid #435;
    border-radius: 1em;
    padding: 0.25em 0.5em;
    color: #435;
  }
</style>