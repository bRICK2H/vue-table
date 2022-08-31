<template>
	<VCheckBox
		v-bind="component.props"
		v-model="component.value"
		labelPosition="left"
		:margin="0"
		:classes="['v-active']"
	/>
</template>

<script>
import VCheckBox from '@inowave/izi-checkbox'

export default {
	name: 'vSelfStatus',
	components: {
		VCheckBox
	},
	props: {
		component: {
			type: Object,
			default: () => ({})
		},
	},
}
</script>

<style lang="scss">
	.v-active {
		.v-checkbox-label {
			font-size: 13px;
			font-weight: 600;
			color: #a2a2b9;
		}
	}
</style>