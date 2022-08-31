<template>
	<span>
		{{ formatDate }}
	</span>
</template>

<script>

export default {
	name: 'VDate',
	props: {
		component: {
			type: Object,
			default: () => ({})
		}
	},
	computed: {
		formatDate() {
			return this.component.value
				? new Date(this.component.value.slice(0, 10)).toLocaleDateString()
				: ''
		}
	}
}
</script>