<script lang="ts">
  import { mdiMenu } from '@mdi/js';

  import { showDrawer } from './AppLayout.svelte';
  import Breadcrumb from './Breadcrumb.svelte';
  import Button from './Button.svelte';
  import { browser } from '../utils/env';
  import { cls } from '../utils/styles';
  import { getComponentClasses } from './theme';

  export let title: string | number | Array<string | number> = '';
  export let menuIcon: string | null = mdiMenu;
  let className: string | undefined = undefined;
  export { className as class };

  /**
   * Update head / document.title.  Set to false to disable
   */
  export let head = true;

  const settingsClasses = getComponentClasses('AppBar');

  $: titleString = Array.isArray(title) ? title.filter((x) => x).join(' › ') : title.toString();

  $: if (browser && head) {
    // Appears to be needed for some reactive updates
    document.title = titleString;
  }

  function toggleMenu() {
    $showDrawer = !$showDrawer;
  }
</script>

<header
  class={cls('AppBar', 'px-4 flex items-center relative z-50', settingsClasses.root, className)}
>
  {#if menuIcon}
    <slot name="menuIcon" {toggleMenu} isMenuOpen={$showDrawer}>
      <Button icon={menuIcon} on:click={toggleMenu} class="p-3" />
    </slot>
  {/if}

  {#if $$slots.title}
    <slot name="title" />
  {:else}
    <div class="ml-2 text-lg font-medium">
      {#if typeof title === 'string' || typeof title === 'number'}
        {title}
      {:else}
        <Breadcrumb items={title} class="gap-2" />
      {/if}
    </div>
  {/if}

  <slot />

  <div class="flex-1 grid justify-end">
    <slot name="actions" />
  </div>
</header>

<svelte:head>
  {#if head}
    <title>{titleString}</title>
  {/if}
</svelte:head>
