<script setup lang="ts">
import ItemButton from './Button.vue';

import { ref } from 'vue';
import { Reposition } from '@/quick/reposition';
import { Singleton } from '@/lib/renderer/singleton';

/**
 * Return `true` to hide the context menu.
 */
type Callback = (index: number, value: string) => boolean|void;

type Item = {
	text: string,
	callback: Callback,
	color: string
};

const $left = ref<number>(0);
const $top = ref<number>(0);
const $items = ref<Item[]>([]);
const $visible = ref<boolean>(false);
const $window_node = ref<HTMLElement>();

function Hide() {
	$visible.value = false;
}

function Select(index = -1) {
	if (index == -1)
		return Hide();

	let item = $items.value[index];

	if (item == null)
		return Hide();

	if (item.callback == null ||
		item.callback(index, item.text))
		return Hide();
}

function Show(x0: number, y0: number) {
	let rect =
		$window_node.value
		.getBoundingClientRect();

	let {x, y} =
		Reposition(x0, y0, rect);
		
	$left.value = x;
	$top.value = y;
	$visible.value = true;
}

defineExpose({
	$visible,

	Select,
	Show,

	Clear() {
		$items.value = [];
	},

	Add(item: Item) {
		$items.value.push(item);
	},

	ShowFromEvent(event: Event): void {
		if (event instanceof TouchEvent)
			Show(
				event.touches[0].clientX,
				event.touches[0].clientY
			);

		else if (event instanceof MouseEvent)
			Show(
				event.clientX,
				event.clientY
			);
	}
});

defineOptions(Singleton({
	name: "ContextMenu"
}));
</script>

<template>
  <div
    class="root absolute top-0 left-0 w-full h-full pointer-events-auto"
    :visible="$visible"
    @pointer-press="Select(-1)"
  >
    <div
      ref="$window_node"
      class="window absolute flex flex-fixed flex-col bg-white
			wp-192 border-[1px] border-black shadow-xl shrinkable overflow-y-auto max-h-[192px]"
      :style="`left: ${$left}px; top: ${$top}px`"
      :visible="$visible"
    >
      <ItemButton
        v-for="(item, index) in $items"
        :key="index"
        :text="item.text"
        :color="item.color"
        @pointer-press="Select(index)"
      />
    </div>
  </div>
</template>

<style scoped>
@import '@/styles/size.css';
@import '@/styles/flex.css';
@import '@/styles/behavior.css';

@tailwind base;
@tailwind components;
@tailwind utilities;

.root[visible="false"],
.root[visible="false"] * {
	pointer-events: none !important;
}

.window[visible="false"] {
	@apply shrink;
}

</style>
