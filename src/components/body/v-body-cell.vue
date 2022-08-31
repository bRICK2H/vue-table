<template>
	<div v-show="!hidden"
		class="v-body-cell"
		:ref="bodyCellRef"
		:style="setStyleBodyCell"
		@click="handler"
	>
		<component v-if="typeof value === 'object' && value.component"
			:is="value.component.name"
			v-bind="{ component: { ...value.component, width } }"
			v-model="value.component.value"
		/>
		<span v-else
			class="v-body-cell-name"
		>
			{{ value }}
		</span>

		<span class="v-body-cell-line"
			:style="setStyleBodyCellLine"
		></span>

	</div>
</template>

<script>
import { bus } from '../../main'
import VTariff from '../body-templates/v-tariff'
import VStatus from '../body-templates/v-status'
import VAction from '../body-templates/v-action'
import VActive from '../body-templates/v-active'
import VDate from '../body-templates/v-date'
import VRate from '../body-templates/v-rate'
import VIcon from '../body-templates/v-icon'
import VAmount from '../body-templates/v-amount'
import VPriceEditNumeric from '../body-templates/unique/v-price-edit-numeric'
import VPriceEditTitle from '../body-templates/unique/v-price-edit-title'
import VTypeTransaction from '../body-templates/v-typeTransaction'

export default {
	name: 'VBodyCell',
	components: {
		VTariff,
		VStatus,
		VAction,
		VActive,
		VAmount,
		VImagery: () => import('../body-templates/v-imagery'),
		VDate,
		VRate,
		VIcon,
		VTypeTransaction,
		VPriceEditNumeric,
		VPriceEditTitle,
	},
	props: [
		'side',
		'vLine',
		'value',
		'width',
		'height',
		'hidden'
	],
	data: () => ({
		bodyCellRef: '',
		cellHeight: 0,
		top: null,
	}),
	computed: {
		isHandler() {
			return this.value.component?.handler && this.value.component.handler !== null
		},
		setStyleBodyCell() {
			return {
				justifyContent: this.side,
				maxWidth: `${this.width}px`,
				minWidth: `${this.width}px`,
				cursor: this.isHandler ? 'pointer' : 'cursor',
			}
		},
		setStyleBodyCellLine() {
			return this.top !== null
				? { top: `-${this.top}px`, height: `${this.height}px` }
				: null
		}
	},
	methods: {
		handler() {
			if (this.isHandler) {
				bus.$emit('handler', this.value.component.handler)
			}
		},
	},
	watch: {
		async height(v) {
			await this.$nextTick()
			const top = this.height - (this.cellHeight + 40)
			this.top = top === 0 ? 16 : (20 - top) + 20
		}
	},
	created() {
		this.bodyCellRef = `v-body-cell-ref:${String(Math.random()).slice(2, 10)}`
	},
	mounted() {
		if (this.vLine) {
			const bodyCellRef = this.$refs[this.bodyCellRef]
	
			if (bodyCellRef) {
				this.cellHeight = bodyCellRef.offsetHeight
			} else {
				this.$nextTick(() => {
					this.cellHeight = bodyCellRef.offsetHeight
				})
			}
		}
	}
}
</script>

<style lang="scss">
	.v-body-cell {
		display: flex;
		font-size: 14px;
		font-weight: 400;
		flex: 1 1 auto;
		position: relative;
		
		&:not(:last-of-type) { 
			margin: auto 35px auto 0;

			.v-body-cell-line {
				background: #f2f2fb;
			}
		}
		&:last-of-type {
			margin: auto 0 auto auto;
		}
	}
	.v-body-cell-name {
		text-overflow: ellipsis;
		overflow: hidden;
		white-space: nowrap;
	}
	.v-body-cell-line {
		width: 1px;
		position: absolute;
		right: calc(-35px / 2);
	}
	.v-body-cell-more-box {
		display: flex;
		flex-direction: column;
	}
	.v-body-cell-more-item {
		
		&:not(:first-of-type) {
			font-size: 12px;
			color: #a2a2b9;
			font-weight: 500;
		}
		&:not(:last-of-type) {
			margin-bottom: 5px;
		}
	}

	.v-body-cell-file {
		font-size: 12px;
		font-weight: 500;
		color: #a2a2b9;
		display: flex;
		flex-direction: column;
		align-items: center;
		text-decoration: none;
		transition: .2s;

		&:hover {
			filter: brightness(.6);
		}
	}
	.v-body-cell-pdf {
		width: 20px;
		height: 24px;
		background: url('../../assets/img/png/pdf.png') no-repeat center / cover;
		margin-bottom: 7px;
	}

	// sum
	.v-body-cell-sum {
		font-size: 14px;
		font-weight: 500;
		color: #4fd1c5;

		&--negative {
			color: #ec4848;
		}
	}
</style>