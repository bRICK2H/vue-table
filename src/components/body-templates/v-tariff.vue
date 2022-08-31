<template>
	<div class="v-tariff">
		<span class="v-tariff-title">
			{{ value }}
		</span>
		<span class="v-title-price">
			{{ component.props.price }} {{ component.props.postfix }} {{ getPer }}
		</span>
		
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
		},
		value() {
			const result = []
			const amount = this.component.value
			const arrAmount = String(amount).replace(/[\D]/, '').split('')

			while(arrAmount.length > 3) {
				result.unshift(...[' ', ...arrAmount.splice(-3)])
			}

			result.unshift(...arrAmount)
			return result.join('')
		}
	}
}
</script>

<style lang="scss">
	.v-tariff {
		font-size: 14px;
		color: #1F1F33;
		font-weight: 500;
	}
	.v-tariff-title {
		text-overflow: ellipsis;
    	overflow: hidden;
    	white-space: nowrap;
	}
	.v-title-price {
	}
</style>