<template>
	<div class="v-table-header"
		:ref="headerRef"
	>
		<VHeaderCell
			v-for="(h, i) of header"
			:key="i"
			:hData="h"
			:index="i"
			:body="body"
			:transformRef="transformRef"
			:sortOptions="sortOptions"
			:filterOptions="filterOptions"
			@get-header-cell-width="getHeaderCellWidth"
			@sort-data="options => sortData(options, i)"
			@toggle-filter="options => toggleFilter(options, i)"
		/>
	</div>
</template>

<script>
import VHeaderCell from './v-header-cell.vue'

export default {
	name: 'VHeader',
	components: {
		VHeaderCell
	},
	props: {
		header: {
			type: Array,
			default: () => ([])
		},
		body: {
			type: Array,
			default: () => ([])
		}
	},
	data: () => ({
		headerRef: '',
		cellHeights: [],
		sortOptions: {},
		filterOptions: {},
	}),
	methods: {
		toggleFilter({ component }, index) {
			this.$set(this.filterOptions, 'index', index)
			this.$set(this.sortOptions, 'index', index)
			this.$set(this.filterOptions, 'component', component)
		},
		transformRef(str) {
			return `${str}:${String(Math.random()).slice(2, 10)}`
		},
		getHeaderCellWidth(value) {
			this.cellHeights.push(value)
		},
		sortData({ type, related, sorted }, index) {
			this.$set(this.sortOptions, 'index', index)
			this.$set(this.filterOptions, 'index', index)
			this.$set(this.sortOptions, 'sorted', sorted)
			this.$emit('sort-data', { type, related, sorted, index })
		},
	},
	created() {
		this.headerRef = this.transformRef('v-header')
	},
	mounted() {
		this.$emit('get-header-height', this.$refs[this.headerRef].offsetHeight)
		this.$emit('get-header-cell-width', this.cellHeights)
	}
}
</script>

<style lang="scss">
	.v-table-header {
		width: 100%;
		display: flex;
		padding: 10px 50px 10px 40px;
		justify-content: flex-start;
		background-color: #f6f6fb;
	}
</style>