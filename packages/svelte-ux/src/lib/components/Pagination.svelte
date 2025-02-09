<script lang="ts">
  import { mdiChevronLeft, mdiChevronRight, mdiMenuDown, mdiPageFirst, mdiPageLast } from '@mdi/js';

  import type paginationStore from '../stores/paginationStore';
  import { cls } from '../utils/styles';
  import type { StoresValues } from '../types/typeHelpers';

  import Button from './Button.svelte';
  import Icon from './Icon.svelte';
  import Menu from './Menu.svelte';
  import MenuItem from './MenuItem.svelte';
  import Toggle from './Toggle.svelte';
  import Tooltip from './Tooltip.svelte';
  import { getComponentClasses } from './theme';
  import { getSettings } from './settings';

  type Pagination = ReturnType<typeof paginationStore>;

  export let pagination: Pagination;
  export let perPageOptions = [10, 25, 50, 100, 1000];
  export let hideSinglePage = false;
  export let format: (pagination: StoresValues<Pagination>) => string = (pagination) => {
    return `${$pagination.from.toLocaleString()}-${$pagination.to.toLocaleString()} of ${$pagination.total.toLocaleString()}`;
  };

  type ShowComponent =
    | 'prevPage'
    | 'nextPage'
    | 'firstPage'
    | 'lastPage'
    | 'pagination'
    | 'perPage'
    | 'actions';
  export let show: ShowComponent[] = ['prevPage', 'pagination', 'nextPage'];

  export let classes: {
    root?: string;
    buttons?: string;
    pagination?: string;
    perPage?: string;
  } = {};
  const { format: formatValue } = getSettings();
  const settingsClasses = getComponentClasses('Pagination');
</script>

{#if $pagination.totalPages > 1 || !hideSinglePage}
  <div
    class={cls(
      'Pagination',
      'flex items-center gap-1',
      settingsClasses.root,
      classes.root,
      $$props.class
    )}
  >
    {#each show as component}
      {#if component === 'actions'}
        <slot name="actions" />
      {/if}

      {#if component === 'firstPage'}
        <Tooltip title="First page" offset={2}>
          <Button
            icon={mdiPageFirst}
            on:click={pagination.firstPage}
            disabled={$pagination.isFirst}
            aria-label="First Page"
            class={cls('p-2', settingsClasses.buttons, classes.buttons)}
          />
        </Tooltip>
      {/if}

      {#if component === 'prevPage'}
        <Tooltip title="Previous page" offset={2}>
          <Button
            icon={mdiChevronLeft}
            on:click={pagination.prevPage}
            disabled={$pagination.isFirst}
            aria-label="Previous Page"
            class={cls('p-2', settingsClasses.buttons, classes.buttons)}
          />
        </Tooltip>
      {/if}

      {#if component === 'nextPage'}
        <Tooltip title="Next page" offset={2}>
          <Button
            icon={mdiChevronRight}
            on:click={pagination.nextPage}
            disabled={$pagination.isLast}
            aria-label="Next Page"
            class={cls('p-2', settingsClasses.buttons, classes.buttons)}
          />
        </Tooltip>
      {/if}

      {#if component === 'lastPage'}
        <Tooltip title="Last page" offset={2}>
          <Button
            icon={mdiPageLast}
            on:click={pagination.lastPage}
            disabled={$pagination.isLast}
            aria-label="Last Page"
            class={cls('p-2', settingsClasses.buttons, classes.buttons)}
          />
        </Tooltip>
      {/if}

      {#if component === 'perPage'}
        <div class={cls('text-sm text-center', settingsClasses.perPage, classes.perPage)}>
          Per page:
          <Toggle let:on={open} let:toggle>
            <span>
              <Button on:click={toggle}>
                {$pagination.perPage}
                <Icon path={mdiMenuDown} />
              </Button>

              <Menu
                {open}
                on:close={toggle}
                autoPlacement
                offset={12}
                classes={{ menu: 'group p-1' }}
              >
                {#each perPageOptions ?? [] as option}
                  <MenuItem
                    class="justify-end"
                    selected={$pagination.perPage === option}
                    on:click={() => pagination.setPerPage(option)}
                  >
                    {$formatValue(option, 'integer')}
                  </MenuItem>
                {/each}
              </Menu>
            </span>
          </Toggle>
        </div>
      {/if}

      {#if component === 'pagination'}
        <slot name="pagination" pagination={$pagination}>
          <div class={cls('text-sm tabular-nums', settingsClasses.pagination, classes.pagination)}>
            {format($pagination)}
          </div>
        </slot>
      {/if}
    {/each}
  </div>
{/if}
