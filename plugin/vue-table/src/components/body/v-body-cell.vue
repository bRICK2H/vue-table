<template>
	<div class="v-body-cell"
		:style="{ minWidth: `${hcellWidth}px`, maxWidth: `${hcellWidth}px`, justifyContent: side }"
	>		
	
		<component v-if="typeof value === 'object' && value.component"
			:is="value.component.name"
			v-bind="{ component: value.component }"
		/>
		<span v-else
			class="v-body-cell-name"
		>
			{{ value }}
		</span>

	</div>
</template>

<script>
import VTariff from '../body-templates/v-tariff'
import VStatus from '../body-templates/v-status'
import VAction from '../body-templates/v-action'
import VActive from '../body-templates/v-active'
import VDetail from '../body-templates/v-detail'
import VDate from '../body-templates/v-date'

export default {
	name: 'VBodyCell',
	components: {
		VTariff,
		VStatus,
		VAction,
		VActive,
		VDetail,
		VDate
	},
	props: {
		value: null,
		side: {
			type: String,
			default: 'flex-start'
		},
		hcellWidth: {
			type: Number,
			default: 0
		},
	},
}
</script>

<style lang="scss">
	.v-body-cell {
		display: flex;
		font-size: 14px;
		font-weight: 400;
		flex: 1 1 auto;
		
		&:not(:last-of-type) {
			margin-right: 35px;
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

	// file
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