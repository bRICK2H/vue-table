<template>
	<div class="v-body-row"
		:ref="bodyRowRef"
		:class="{ 'v-body-row--scrollable': this.scrollable }"
	>
		<slot />
		<div class="v-body-cell-box">
			<span v-show="detail"
				class="v-body-toggle-detail"
				:class="{ 'v-body-toggle-detail--active': active }"
				@click="$emit('toggle-detail', !active)"
			></span>
			
			<div class="v-body-cell-content"
				:class="{ 'v-body-cell-content--scrollable': this.scrollable }"
			>
				<VBodyCell v-for="({ side, width, value, hidden }, i) in filterRow"
					:key="i"
					:value="value"
					:vLine="vLine"
					:side="side"
					:width="width"
					:height="rowHeight"
					:hidden="hidden"
				/>
			</div>
		</div>

		<!-- Detail -->
		<transition name="detail">
			<div v-if="detail && active && getDetail"
				class="v-body-detail-box"
			>
				<component :is="getDetail.name"
					v-bind="{ component: getDetail }"
				/>
			</div>
		</transition>
	</div>
</template>

<script>
import VBodyCell from './v-body-cell.vue'
import VDetail from '../body-templates/v-detail'

export default {
	name: 'VBodyRow',
	components: {
		VBodyCell,
		VDetail,
	},
	props: [
		'row',
		'vLine',
		'index',
		'detail',
		'active',
		'header',
		'autoWidth',
		'scrollable'
	],
	data: () => ({
		bodyRowRef: '',
		rowHeight: 0,
	}),
	computed: {
		filterRow() {
			const relatedSearch = this.search?.related ?? null
			return this.header.map(curr => {
				const {
					width,
					related,
					side = 'flex-start',
					hidden = false
				} = curr

				return {
					side,
					hidden,
					related,
					width: this.autoWidth[related],
					value: this.row?.[related] ?? '',
				}
			})
		},
		getDetail() {
			return this.row.detail?.component
		}
	},
	created() {
		this.bodyRowRef = `v-body-row-ref:${String(Math.random()).slice(2, 10)}`
	},
	mounted() {
		if (this.vLine) {
			const bodyRowRef = this.$refs[this.bodyRowRef]
	
			if (bodyRowRef) {
				this.rowHeight = bodyRowRef.offsetHeight
			} else {
				this.$nextTick(() => {
					this.rowHeight = bodyRowRef.offsetHeight
				})
			}
		}
	}
}
</script>

<style lang="scss">
	.v-body-row {
		position: relative;
		// transition: .1s;

		&--scrollable {
			width: calc(100% - 5px);
		}

		&:nth-child(even) {
			.v-body-row--background {
				background: rgba(237, 237, 247, .2);
			}
		}
		&:not(:last-of-type) {
			// border-bottom: 2px solid #f6f6fb;
		}
		&:hover {
			.v-body-row--background {
				border: 1px solid rgb(237, 237, 247);
				background: rgba(237, 237, 247, .6);
			}
		}
		
		
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
		background: rgba(237, 237, 247, .6);
		border-radius: 8px;
	}

	.v-body-cell-content {
		display: flex;
		justify-content: flex-start;
		align-items: center;
		padding: 20px 35px;

		&--scrollable {
			padding: 20px 20px 20px 30px;
		}
	}

	.detail-enter-active {
		animation: detail-active 1s;

		@keyframes detail-active {
			0% { height: 0; opacity: 0; transform: translateY(-20px); }
			30% { transform: translateY(0); }
			100% { height: 100% }
		}
	}
</style>