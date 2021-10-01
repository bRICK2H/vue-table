<template>
	<p class="v-detail">
		{{ component.props.title }}
	</p>
</template>

<script>
export default {
	name: 'vSelfDetail',
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