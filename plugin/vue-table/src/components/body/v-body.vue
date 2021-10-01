<template>
	<div class="v-body-container"
		:style="setStyleMaxHeightBodyContainer"
	>
		<div class="v-body-content">
			<transition-group name="body-row" tag="div">
				<VBodyRow v-for="(row, i) of data.bf"
					:key="row.id"
					:row="row"
					:side="getPosition"
					:detail="$attrs.detail"
					:active="i === currIndex ? active : false"
					:headerCellWidth="$attrs.headerCellWidth"
					@toggle-detail="value => toggleDetail(value, i)"
				/>
			</transition-group>
		</div>
	</div>
</template>

<script>
import VBodyRow from './v-body-row.vue'

export default {
	name: 'VBody',
	components: {
		VBodyRow
	},
	props: {
		headerHeight: {
			type: Number,
			default: 0
		},
		data: {
			type: Object,
			default: () => ({})
		},
		height: {
			type: [String, Number],
			default: 500
		}
	},
	data: () => ({
		active: false,
		currIndex: null
	}),
	computed: {
		setStyleMaxHeightBodyContainer() {
			const margin = 5
			return { 
				maxHeight: `calc(${this.height} - ${this.headerHeight + (margin * 2)}px)`,
				margin: `${margin}px ${margin}px ${margin}px ${margin * 2}px`
			}
		},
		getPosition() {
			return this.data.h.map(curr => curr.side)
		}
	},
	methods: {
		toggleDetail(value, i) {
			this.currIndex = i
			this.active = value
		}
	}
}
</script>

<style lang="scss">
	.v-body-container {
		overflow-y: auto;
	
		&::-webkit-scrollbar {
			width: 8px;
		}
		&::-webkit-scrollbar-thumb {
			background: #eeedf7;
			border-radius: 8px;
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
</style>