<template>
	<div class="v-table-wrapper"
		:ref="tableRef"
		:style="setStyleTableContainer"
	>
		<VPagination v-show="isPagination && !scrollable"
			:ref="paginationRef"
			:paging="paging"
			:style="setStylePagination"
		/>
		
		<div class="v-table-container">
			<!-- Header -->
			<VHeader v-show="getHeader"
				:header="getHeader"
				:body="this.data.b"
				:scrollable="scrollable"
				:isClearFilter="isClearFilter"
				:bodyScrollX="bodyScrollX"
				:sticky="sticky"
				@get-header-height="value => headerHeight = value"
				@get-auto-header-cell-width="values => headerCellWidth = values"
				@handler-sort="handlerSort"
			/>
			
			<!-- Body -->
			<VBody
				:height="height"
				:minRows="minRows"
				:heightRows="heightRows"
				:maxHeight="maxHeight"
				:headerHeight="headerHeight"
				:headerCellWidth="headerCellWidth"
				:bodyLabel="bodyLabel"
				:data="data"
				:detail="detail"
				:vLine="vLine"
				:search="search"
				:getStyleValue="getStyleValue"
				:scrollable="scrollable"
				@scroll-x="value => bodyScrollX = value"
			/>
		</div>

		<VPagination v-show="isPagination && scrollable"
			:ref="paginationRef"
			:paging="paging"
			:style="setStylePagination"
		/>
	</div>
</template>

<script>
import { bus } from './main'
import VHeader from './components/header/v-header.vue'
import VBody from './components/body/v-body.vue'
import VPagination from '@inowave/izi-pagination'

export default {
	name: 'VTable',
	components: {
		VHeader,
		VBody,
		VPagination
	},
	props: {
		width: {
			type: [String, Number],
			default: 'auto'
		},
		minWidth: {
			type: [String, Number],
			default: 0
		},
		height: {
			type: [String, Number],
			default: 'auto'
		},
		scrollable: {
			type: Boolean,
			default: false
		},
		minRows: {
			type: Number,
			default: 1
		},
		heightRows: {
			type: Number,
			default: 85
		},
		isClearFilter: {
			type: Boolean,
			default: false
		},
		isPagination: {
			type: Boolean,
			default: false
		},
		vLine: {
			type: Boolean,
			default: false
		},
		data: {
			type: Object,
			default: () => ({
				h: [],
				b: [],
				bf: [],
			})
		},
		detail: {
			type: Boolean,
			default: false
		},
		paging: {
			type: Object,
			default: () => ({
				page: 0,
				size: 20,
				results: 0
			})
		},
		sticky: {
			type: Object,
			default: () => ({
				header: null,
				pagination: null,
			})
		},
		search: {
			type: Object,
			default: () => ({
				value: '',
				related: null
			})
		},
		bodyLabel: {
			type: String,
			default: 'Cовпадений не найдено'
		}
	},
	data: () => ({
		tableRef: '',
		paginationRef: '',
		pagMargin: 0,
		maxHeight: 0,
		headerHeight: 0,
		headerCellWidth: {},
		sortOptions: {},
		bodyScrollX: 0,
		timeoutID: 0,
		filters: { some: {}, every: {} },
	}),
	computed: {
		setStyleTableContainer() {
			return {
				maxWidth: this.getStyleValue(this.width),
				minWidth: this.getStyleValue(this.minWidth),
			}
		},
		getHeader() {
			return this.data.h.length
				? this.data.h
				: null
		},
		setStylePagination() {
			const sticky = this.sticky['pagination']
			const padding = this.scrollable
				? { paddingBottom: `${this.pagMargin}px` }
				: { paddingBottom: '20px' }

			return sticky !== null
				? {
					position: 'sticky',
					top: `${sticky}px`,
					zIndex: 10,
					padding: '20px 0'
				}
				: padding
		}
	},
	methods: {
		getStyleValue(value) {
			if (!value) return 'auto'
			const type = typeof value

			switch (type) {
				case 'string': {
					return isNaN(parseFloat(value[value.length - 1]))
						? value
						: `${value}px`
				}

				case 'number': {
					return `${value}px`
				}
			}
		},
		setMaxHeightTable(elementFrom, elementTo) {
			const paddingTable = 15
			const heightHeaderTable = this.headerHeight
			const heightHeaderMain = document.querySelector('.iziheader')?.offsetHeight ?? 72
			const heightPagination = this.isPagination
				? this.$refs[this.paginationRef]?.$el?.offsetHeight ?? 48
				: 0
			const marginPagination = this.isPagination
				? this.pagMargin * 2
				: 20
			const startPoint = elementFrom.offsetTop
			const lastPoint = elementTo.nextElementSibling.offsetTop

			this.maxHeight = lastPoint - (startPoint + heightHeaderTable + heightHeaderMain + paddingTable + heightPagination + marginPagination)
		},
		sortData({ type, related, sorted }) {
			this.data.bf.sort(({ [related]: valA }, { [related]: valB }) => {
				let a = valA?.component ? valA.component.value : valA
				let b = valB?.component ? valB.component.value : valB

				switch (type) {
					case 'number': {
						return sorted
							? b - a
							: a - b
					}

					case 'date': {
						return sorted
							? new Date(b) - new Date(a)
							: new Date(a) - new Date(b)
					}

					case 'string': {
						return sorted
							? b.localeCompare(a)
							: a.localeCompare(b)
					}
				}

			})

		},
		filterData({ value, related }) {
			const isArrayRelated = Array.isArray(related)

			if (isArrayRelated) {
				related.forEach(c_related => {
					this.$set(this.filters['some'], c_related, value)
					if (!value) this.$delete(this.filters['some'], c_related)
				})
			} else {
				this.$set(this.filters['every'], related, value)

				if (related in this.filters.every && (!value || !value.length)) {
					this.$delete(this.filters['every'], related)
				}
			}

			const defineValue = (curr, rel) => {
				return typeof curr[rel] !== 'object'
					? String(curr[rel])
					: String(curr[rel].component.value)
			}
			const defineFilterName = rel => {
				return this.data.h.find(h => h.related === rel)?.filterType ?? 'search'
			}

			const filterValue = (fo, method, curr) => {
				return Object.entries(fo)[method](c_fo => {
					const [related_name, value] = c_fo

					switch (defineFilterName(related_name)) {

						case 'search': {
							return defineValue(curr, related_name).toLowerCase().includes(String(value).toLowerCase())
						}

						case 'date_range': {
							const [start, end] = value
							const value_cell = new Date(new Date(defineValue(curr, related_name).slice(0, 10)).toDateString())

							return value_cell >= new Date(start) && value_cell <= new Date(end) && value.length
						}

					}
				})
			}

			this.data.bf = this.data.b.filter(curr => {
				const isEvery = filterValue(this.filters.every, 'every', curr)
				const isSome = Object.keys(this.filters.some).length
					? filterValue(this.filters.some, 'some', curr)
					: true

				return isEvery && isSome
			})
		},
		handlerSort(options) {
			const { use } = options

			use !== 'global'
				? this.sortData(options)
				: this.$emit('sort-data', options)
		}
	},
	watch: {
		'data.b': {
			deep: true,
			immediate: true,
			async handler(data) {
				this.data.bf = data

				if (data.length && this.scrollable) {
					await this.$nextTick()
					const tableRef = this.$refs[this.tableRef]
	
					if (tableRef) {
						let elementTo = tableRef
				
						while(elementTo.nextElementSibling === null) {
							elementTo = elementTo.parentElement
						}
						
						if (elementTo) {
							this.setMaxHeightTable(tableRef, elementTo)
							// window.addEventListener('resize', e => this.setMaxHeightTable(tableRef, elementTo))
						}
					}
				}
			}
		},
		isPagination: {
			immediate: true,
			handler(isPag) {
				if (isPag) {
					this.pagMargin = 20
				} 
			}
		},
		search(options) {
			if (options?.related || options?.related.length) {
				clearTimeout(this.timeoutID)
				this.timeoutID = setTimeout(() => {
					this.filterData(options)
				}, 300)
			}
		},
	},
	created() {
		this.tableRef = `v-table-wrapper:${String(Math.random()).slice(2, 10)}`
		this.paginationRef = `v-pagination:${String(Math.random()).slice(2, 10)}`

		bus.$on('handler-filter', options => {
			const { use } = options
			
			if (use !== 'global') {
				clearTimeout(this.timeoutID)
				this.timeoutID = setTimeout(() => {
					this.filterData(options)
				}, 300)
			} else {
				this.$emit('filter-data', options)
			}
		})
		bus.$on('handler', options => {
			this.$emit('handler', options)
		})
	},
}
</script>

<style lang="scss">
	@import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600&display=swap');

	.v-table-container {
		width: 100%;
		border-radius: 12px;
		margin-bottom: 20px;

		& * {
			font-family: 'Inter', sans-serif;
		}
	}
</style>