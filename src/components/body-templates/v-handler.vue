<template>
	<span class="v-self-handler">
		{{ component.value }}
	</span>
</template>

<script>
export default {
	name: 'VSelfHandler',
	props: {
		component: {
			type: Object,
			default: () => ({})
		},
	},
}
</script>

<style lang="scss">
	.v-self-handler {
		// cursor: pointer;
	}
</style>