<script lang="ts">
  import { cls } from '../utils/styles';
  import { getComponentClasses } from './theme';

  const settingsClasses = getComponentClasses('SectionDivider');
</script>

<div
  {...$$props}
  class={cls('SectionDivider', 'flex items-center my-2', settingsClasses.root, $$props.class)}
>
  <div class="flex-1 border h-px rounded-full mr-2" />
  <div class="font-medium text-surface-content border px-4 rounded-full bg-surface-100 shadow">
    <slot />
  </div>
  <div class="flex-1 border h-px rounded-full ml-2" />
</div>
