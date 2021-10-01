<template>
	<div class="v-tariff">
		<span class="v-tariff-title">
			{{ component.value }}
		</span>
		<p class="v-title-price">
			{{ component.props.price }} {{ component.props.postfix }} {{ getPer }}
		</p>
		
	</div>
</template>

<script>
export default {
	name: 'VSelfCurrTariff',
	props: {
		component: {
			type: Object,
			default: () => ({})
		}
	},
	computed: {
		getPer() {
			return this.component.props.per ? `/ ${this.component.props.per}` : ''
		}
	}
}
</script>

<style lang="scss">
	.v-tariff {
		font-size: 14px;
		color: #1F1F33;
	}
	.v-tariff-title {
		text-overflow: ellipsis;
    	overflow: hidden;
    	white-space: nowrap;
	}
	.v-title-price {
		font-weight: 500;
	}
</style>