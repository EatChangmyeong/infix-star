<script lang="ts">
  import type { Entry } from './table.ts';
  import { moduleMap, packageVersionMap } from './table.ts';
  export let
    entry: Entry,
    mod: string,
    src: boolean = false;

  let
    docLink: string,
    srcLink: string;
  $: docLink = getDocLink(entry, mod);
  $: srcLink = src ? getSrcLink(entry) : '';

  function getDocLink(entry: Entry, mod: string): string {
    if(!moduleMap.has(mod))
      return '';
    const pack = moduleMap.get(mod)!;
    if(!packageVersionMap.has(pack))
      return '';

    const version = packageVersionMap.get(pack)!;

    const
      modEscaped = mod.replace(/\./g, '-'),
      escaped = entry.name.replace(/\W/g, x => `-${x.codePointAt(0)}-`);
    return `https://hackage.haskell.org/package/${pack}-${version}/docs/${modEscaped}.html#v:${escaped}`;
  }
  function getSrcLink(entry: Entry): string {
    const mod = entry.defined;
    if(!moduleMap.has(mod))
      return '';
    const pack = moduleMap.get(mod)!;
    if(!packageVersionMap.has(pack))
      return '';
    const version = packageVersionMap.get(pack)!;

    const escaped = encodeURIComponent(entry.name);
    return `https://hackage.haskell.org/package/${pack}-${version}/docs/src/${mod}.html#${escaped}`;
  }
</script>

<span>
  {#if docLink}
    <a href={docLink}>{mod}</a>
  {:else}
    {mod}
  {/if}
  {#if srcLink}
    <a class="small" href={srcLink}>(src)</a>
  {/if}
</span>

<style>
  span {
    font-family: 'Noto Sans Mono', monospace;
    font-style: normal;
  }
</style>