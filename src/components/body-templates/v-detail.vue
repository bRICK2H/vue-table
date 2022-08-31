<template>
	<p class="v-detail">
		{{ component.props.title }}
	</p>
</template>

<script>
export default {
	name: 'vDetail',
	props: {
		component: {
			type: Object,
			default: () => ({})
		}
	}
}
</script>

<style lang="scss">
</style>