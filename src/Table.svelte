<script lang="ts" context="module">
  import { writable } from 'svelte/store';
  export const selected = writable<[number, number, Entry] | undefined>(undefined);
</script>

<script lang="ts">
  import Td from './Td.svelte';
  import Details from './Details.svelte';
  import type { Entry } from './table.ts';
  import { table } from './table.ts';

  const indexedTable = table.map((x, i) => [i - 1, x]).reverse();
</script>

<table>
  <thead>
    <tr>
      <th></th>
      <th>infixl</th>
      <th>infix</th>
      <th>infixr</th>
    </tr>
  </thead>
  <tbody>
  {#each indexedTable as [prec, row]}
    <tr>
      <th>{prec}</th>
      {#each row as entries, assoc}
        {#if prec == 9 && assoc == 0}
          <Td {entries} precedence={prec} associativity={assoc}>
            Default fixity
          </Td>
        {:else}
          <Td {entries} precedence={prec} associativity={assoc} />
        {/if}
      {/each}
    </tr>
  {/each}
  </tbody>
</table>

{#if $selected}
  <Details selected={$selected} />
{/if}

<style>
  table {
    margin: 0 auto;
    border-collapse: collapse;
  }
  th {
    border: 1px solid #fff;
    padding: 0.25rem;
    font-family: 'Noto Sans Mono', monospace;
    font-style: normal;
    font-weight: normal;
  }
  thead th {
    text-align: center;
  }
  thead th:first-child {
    border: none;
  }
  thead th:not(:first-child), tbody th {
    color: #fff;
    background-color: #435;
  }
  tbody th {
    text-align: right;
  }
  tr {
    background-color: #ffffff80;
  }
  tr:nth-child(2n+3) {
    background-color: #e8e8e880;
  }
</style>