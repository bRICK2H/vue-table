<template>
	<div class="v-table-header"
		:style="setStyleHeader"
		:class="{ 'v-table-header--scrollable': scrollable }"
		:ref="headerRef"
	>
		<VHeaderCell
			v-for="(h, i) of header"
			:key="h.related"
			:hData="h"
			:body="body"
			:transformRef="transformRef"
			:sortOptions="sortOptions"
			:filterOptions="filterOptions"
			:isClearFilter="isClearFilter"
			@get-auto-header-cell-width="getAutoHeaderCellWidth"
			@handler-sort="options => handlerSort(options)"
			@toggle-filter="options => toggleFilter(options, h.related)"
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
	props: [
		'body',
		'header',
		'sticky',
		'scrollable',
		'bodyScrollX',
		'isClearFilter'
	],
	data: () => ({
		headerRef: '',
		cellHeights: {},
		sortOptions: {},
		filterOptions: {},
	}),
	computed: {
		setStyleHeader() {
			const sticky = this.sticky['header']
			return sticky !== null
				? {
					position: 'sticky',
					top: `${sticky}px`,
					zIndex: 10
				}
				: null
		}
	},
	methods: {
		toggleFilter({ component }, related) {
			this.$set(this.sortOptions, 'related', related)
			this.$set(this.filterOptions, 'related', related)
			this.$set(this.filterOptions, 'component', component)
		},
		transformRef(str) {
			return `${str}:${String(Math.random()).slice(2, 10)}`
		},
		getAutoHeaderCellWidth(value) {
			const key = Object.keys(value)
			this.$set(this.cellHeights, key, value[key])
		},
		handlerSort({ use, type, related, sorted }) {
			this.$set(this.filterOptions, 'related', related)
			this.$set(this.sortOptions, 'related', related)
			this.$set(this.sortOptions, 'sorted', sorted)
			this.$emit('handler-sort', { use, type, related, sorted })
		},
	},
	watch: {
		header() {
			this.filterOptions = {}
		},
		bodyScrollX(value) {
			this.$refs[this.headerRef].scrollLeft = value
		}
	},
	created() {
		this.headerRef = this.transformRef('v-header')
	},
	mounted() {
		this.$emit('get-header-height', this.$refs[this.headerRef].offsetHeight)
		this.$emit('get-auto-header-cell-width', this.cellHeights)
	}
}
</script>

<style lang="scss">
	.v-table-header {
		width: 100%;
		height: 70px;
		display: flex;
		padding: 0 35px;
		border-top-left-radius: 12px;
    	border-top-right-radius: 12px;
		justify-content: flex-start;
		background-color: #f2f2fb;
		box-shadow: 0 8px 8px -8px #999,  0 -8px 0 #fff;

		&--scrollable {
			overflow-x: clip;
			overflow: scroll;
			position: relative;

			&::-webkit-scrollbar {
				display: none;
			}
		}

	}
</style>