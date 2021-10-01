<template>
	<div class="v-body-row">

		<div class="v-body-cell-box">
			<span v-show="detail"
				class="v-body-toggle-detail"
				:class="{ 'v-body-toggle-detail--active': active }"
				@click="$emit('toggle-detail', !active)"
			></span>
			
			<div class="v-body-cell-content">
				<VBodyCell v-for="(value, key, i) in filterRow"
					:key="key"
					:value="value"
					:side="side[i]"
					:hcellWidth="headerCellWidth[i]"
				/>
			</div>
		</div>

		<!-- Detail -->
		<transition name="detail">
			<div v-if="detail && active && detailComponent"
				class="v-body-detail-box"
			>
				<component :is="detailComponent.name"
					v-bind="{ component: detailComponent }"
				/>
			</div>
		</transition>
	</div>
</template>

<script>
import VBodyCell from './v-body-cell.vue'

export default {
	name: 'VBodyRow',
	components: {
		VBodyCell
	},
	props: {
		row: {
			type: Object,
			default: () => ({})
		},
		detail: {
			type: Boolean,
			required: true,
		},
		active: {
			type: Boolean,
			default: false
		},
		headerCellWidth: {
			type: Array,
			defaut: () => ([])
		},
		side: {
			type: Array,
			default: () => ([])
		},
	},
	data: () => ({
		delayedKeys: ['id', 'detail'],
		detailComponent: null,
	}),
	computed: {
		filterRow() {
			return Object.entries(this.row)
				.reduce((acc, curr) => {
					const [name, value] = curr

					if (!this.delayedKeys.includes(name)) {
						acc[name] = value
					} else {
						this.detailComponent = value.component
					}

					return acc 
				}, {})
		},
	}
}
</script>

<style lang="scss">
	.v-body-row {
		width: calc(100% - 10px);
		overflow: hidden;
		border-bottom: 2px solid #f6f6fb;
	}
	.v-body-cell-box {
		position: relative;
		z-index: 1;
	}

	.v-body-toggle-detail {
		width: 8px;
		height: 14px;
		background: url('../../assets/img/png/arrow.png') no-repeat center / contain;
		margin-right: 10px;
		cursor: pointer;
		transition: .2s;
		position: absolute;
		top: 50%;
		left: 10px;
		transform: translateY(-50%);

		&--active {
			transform: translateY(-50%) rotate(90deg);
		}
	}

	.v-body-detail-box {
		font-size: 14px;
		font-weight: 400;
		position: relative;
		z-index: 0;
		margin: 0 10px 17px;       
		padding: 12px 16px;
		background: #fafaff;
		border-radius: 8px;                          
	}

	.v-body-cell-content {
		display: flex;
		justify-content: flex-start;
		align-items: center;
		padding: 20px 30px;
	}

	.detail-enter-active {
		animation: detail-active .2s;

		@keyframes detail-active {
			0% { opacity: 0; transform: translateY(-20px); }
		}
	}
	// .detail-leave-active {
	// 	animation: detail-leave .2s;

	// 	@keyframes detail-leave {
	// 		100% { opacity: 0; transform: translateY(-20px); }
	// 	}
	// }
</style>