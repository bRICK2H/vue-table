<template>
	<div class="v-body-container"
		:class="setClassBodyContainer"
		:style="getStyleHeightContainer"
		@scroll="scrollContent($event)"
	>
		<div class="v-body-content">
			<transition-group name="body-row" tag="div">
				<VBodyRow v-for="(row, i) of data.bf"
					:ref="bodyRowRef"
					:key="row.id"
					:row="row"
					:index="i"
					:header="data.h"
					:vLine="vLine"
					:scrollable="scrollable"
					:autoWidth="headerCellWidth"
					:detail="detail"
					:active="row.id === detailId ? active : false"
					@toggle-detail="value => toggleDetail(value, row.id)"
				>
					<span :style="setStyleBackgroundRow"
						class="v-body-row--background"
					></span>
				</VBodyRow>
			</transition-group>
		</div>

		<p v-if="!data.bf.length" class="v-body-content-empty">
			{{ bodyLabel }}
		</p>
	</div>
</template>

<script>
import VBodyRow from './v-body-row.vue'

export default {
	name: 'VBody',
	components: {
		VBodyRow
	},
	props: [
		'data',
		'vLine',
		'detail',
		'height',
		'minRows',
		'bodyLabel',
		'maxHeight',
		'scrollable',
		'heightRows',
		'headerHeight',
		'getStyleValue',
		'headerCellWidth'
	],
	data: () => ({
		bodyRowRef: '',
		rowHeight: 0,
		scrollLeft: 0,
		active: false,
		detailId: null,
	}),
	computed: {
		getStyleHeightContainer() {
			if (this.scrollable) {
				if (this.maxHeight && this.headerHeight && this.data.bf.length) {
					const rowHeight = this.$refs[this.bodyRowRef]?.[0]?.$el?.offsetHeight ?? this.rowHeight
					
					if (rowHeight) {
						this.rowHeight = rowHeight
	
						const totalMaxHeight = this.maxHeight
						const totalMinHeight = rowHeight * this.minRows
	
						return {
							maxHeight: `${totalMaxHeight}px`,
							minHeight: `${totalMinHeight}px`,
							height: `${this.getStyleValue(this.height - this.headerHeight)}`
						}
					} else {
						return this.getCrashStylesHeightContainer
					}
				} else {
					return this.getCrashStylesHeightContainer
				}
			} else {
				return {
					minHeight: `${this.heightRows * this.minRows}px`
				}
			}
		},
		getCrashStylesHeightContainer() {
			return this.scrollable
				? {
					minHeight: `${this.minRows * this.heightRows}px`,
					maxHeight: `${this.minRows * this.heightRows}px`,
				}
				: null
		},
		setStyleBackgroundRow() {
			return { left: `${this.scrollLeft}px`, }
		},
		setClassBodyContainer() {
			return this.scrollable ? 'v-body-container--scrollable' : null
		}
	},
	methods: {
		toggleDetail(value, id) {
			this.detailId = id
			this.active = value
		},
		scrollContent(e) {
			const { target } = e
			this.scrollLeft = target.scrollLeft
			this.$emit('scroll-x', target.scrollLeft)
		}
	},
	watch: {
		async 'data.bf'(bf) {
			this.active = false
			await this.$nextTick()
			const rowHeight = this.$refs[this.bodyRowRef]?.[0]?.$el?.offsetHeight

			if (bf.length && rowHeight) {
				this.rowHeight = rowHeight
			}
		}
	},
	created() {
		this.bodyRowRef = `v-body-row:${String(Math.random()).slice(2, 10)}`
	},
}
</script>

<style lang="scss">
	.v-body-container {
		position: relative;
		border-left: 1px solid #f2f2fb;
		border-right: 1px solid #f2f2fb;

		&--scrollable {
			overflow: auto;
			margin: 5px;
			padding-bottom: 5px;

			&::-webkit-scrollbar {
				width: 8px;
				height: 8px;
			}
			&::-webkit-scrollbar-thumb {
				background: #eeedf7;
				border-radius: 8px;
			}
		}
	}
	.body-row-move {
		transition: transform .3s;
	}
	.body-row-leave-active {
		animation: row-leave .3s;

		@keyframes row-leave {
			100% { opacity: 0; }
		}
	}
	.v-body-content-empty {
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		font-size: 16px;
	}
	.v-body-row--background {
		position: absolute;
		top: 0;
		width: 100%;
		height: 100%;
	}
</style>