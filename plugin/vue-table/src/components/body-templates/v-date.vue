<template>
	<span>
		{{ formatDate }}
	</span>
</template>

<script>
export default {
	name: 'vSelfStatus',
	props: {
		component: {
			type: Object,
			default: () => ({})
		}
	},
	computed: {
		formatDate() {
			const d = new Date(this.component.value)
			const formatNumber = n => n >= 10 ? n : `0${n}`

			return `${formatNumber(d.getDate())}.${formatNumber(d.getMonth() + 1)}.${d.getFullYear()}`
		}
	}
}
</script>