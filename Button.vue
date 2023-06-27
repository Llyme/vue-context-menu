<script setup lang="ts">
defineProps({
	text: {
		type: String,
		default: ''
	},
	color: {
		type: String,
		default: 'black',
		required: false
	}
});
</script>

<template>
  <button
    className="flex-fixed hp-32 -white hover:-very-light-blue
		pointer-events-auto text-sm transition-colors duration-200
		font-medium one-liner w-full overflow-hidden px-[16px]"
    :color="color"
  >
    {{ text }}
  </button>
</template>

<style scoped>
@import '@/styles/flex.css';
@import '@/styles/text.css';
@import '@/styles/color.css';

@tailwind base;
@tailwind components;
@tailwind utilities;

button[color="black"] {
	@apply -black-text;
}

button[color="red"] {
	@apply -gray-text;
}

button[color="green"] {
	@apply -green-text;
}

button[color="gray"] {
	@apply -gray-text;
}
</style>
