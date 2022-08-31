<template>
	<div class="v-rate-container">
		
		<!-- Вариант с адаптацией - одна или 5 звезд -->
		<!-- <ul class="v-rate-box" v-show="component.props.rate">
			<template v-if="!isOneStar">
				<li v-for="n of 5" class="v-rate-outer-item"
					:ref="vRateItemsRef"
				>
					<span class="v-rate-inner-item" :style="setStyleStars(n)"></span>
				</li>
			</template>
			<li v-else class="v-rate-outer-item">
				<span class="v-rate-inner-item"
					:style="setStyleStar"
				></span>
			</li>
			<li class="v-rate-outer-text"
				:ref="vRateLastItemRef"
			>
				{{ modifyRate }}
			</li>
		</ul> -->

	<span class="v-rate-title">
		{{ component.value }}
	</span>
	<!-- Вариант - одна звезда -->
	<ul v-show="component.props.rate"
		class="v-rate-box"
	>
		<li class="v-rate-outer-item">
			<span class="v-rate-inner-item"
				:style="setStyleStar"
			></span>
		</li>
		<li class="v-rate-outer-text"
			:ref="vRateLastItemRef"
		>
			{{ modifyRate }}
		</li>
		<li v-show="component.props.rate_count"
			class="v-rate-outer-count"
		>
		({{ component.props.rate_count }})
		</li>
	</ul>

</div>
</template>

<script>

export default {
	name: 'VRate',
	props: {
		component: {
			type: Object,
			default: () => ({})
		}
	},
	data: () => ({
		vRateItemsRef: '',
		vRateLastItemRef: '',
		widthItems: null,
		isOneStar: false,
	}),
	computed: {
		rate() {
			return +this.component.props.rate
		},
		isRateInteger() {
			return Number.isInteger(this.rate)
		},
		modifyRate() {
			return this.isRateInteger
				? `${this.rate}.0`
				: this.rate.toFixed(1)
		},
		setStyleStar() {
			return {
				background: this.rate === 5
					? '#4fd1c5'
					:	`linear-gradient(
							to right,
							#4fd1c5 0%, #4fd1c5 ${this.rate / 5 * (this.rate > 2.5 ? 75 : 90)}%,
							#fff ${this.rate / 5 * (this.rate > 2.5 ? 75 : 90)}%)`
			}
		}
	},
	methods: {
		setStyleStars(n) {
			if (this.isRateInteger) {
				return n <= this.rate
					? { background: '#4fd1c5' }
					: { background: '#fff' }
			} else {

				if (n < Math.ceil(this.rate)) {
					return { background: '#4fd1c5' }
				} else {
					return n === Math.ceil(this.rate)
						? { background: `linear-gradient(
								to right,
								#4fd1c5 0%, #4fd1c5 ${this.rate / 5 * 100}%,
								#fff ${this.rate / 5 * 100}%)`
							}
						: { background: '#fff' }
				}
			}
		},
		calculatedWidth() {
			if (this.rate) {
				const items = this.$refs[this.vRateItemsRef]
				const lastItemWidth = this.$refs[this.vRateLastItemRef]?.offsetWidth ?? 20
				
				if (items?.length) {
					this.widthItems = [...this.$refs[this.vRateItemsRef]]
						.map(cm => cm.offsetWidth + parseFloat(getComputedStyle(cm).marginRight))
						.reduce((cr, acc) => (acc += cr, acc)) + lastItemWidth
				}
			}
		}
	},
	watch: {
		'component.width'(value) {
			this.isOneStar = value <= this.widthItems
		}
	},
	created() {
		this.vRateItemsRef = `v-rate-items:${String(Math.random()).slice(2, 10)}`
		this.vRateLastItemRef = `v-rate-last-item:${String(Math.random()).slice(2, 10)}`
	},
	mounted() {
		this.calculatedWidth()
		window.addEventListener('resize', this.calculatedWidth)
	}
}
</script>
<style lang="scss">
	.v-rate-container {
		text-overflow: ellipsis;
		overflow: hidden;
	}
	.v-rate-title {
		margin-bottom: 10px;
	}
	.v-rate-box {
		list-style-type: none;
		display: flex;
		align-items: center;
	}
	.v-rate-outer-item {
		min-width: 20px;
		height: 18px;
		position: relative;
		background: #4fd1c5;
		clip-path: polygon(50% 0%, 65% 30%, 98% 35%, 75% 60%, 79% 91%, 50% 80%, 21% 91%, 25% 60%, 2% 35%, 35% 30%);
		position: relative;

		&:not(:last-of-type) {
			margin-right: 4px;
		}
	}
	.v-rate-inner-item {
		width: 16px;
		height: 14px;
		clip-path: polygon(50% 0%, 65% 30%, 98% 35%, 75% 60%, 79% 91%, 50% 80%, 21% 91%, 25% 60%, 2% 35%, 35% 30%);
		position: absolute;
		top: 2px;
		left: 2px;
	}
	.v-rate-outer-text {
		font-size: 14px;
		font-weight: 700;
		color: #1f1f33;
		margin-right: 4px;
	}
	.v-rate-outer-count {
		font-weight: 500;
		font-size: 14px;
		color: #B7B7CC;
	}
</style>