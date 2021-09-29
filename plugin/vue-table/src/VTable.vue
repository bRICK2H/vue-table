<template>
	<div class="v-table-container"
		:style="setStyleTableContainer"
	>

		<!-- Header -->
		<VHeader v-show="getHeader"
			:header="getHeader"
			:body="this.data.b"
			@get-header-height="getHeaderHeight"
			@get-header-cell-width="getHeaderCellWidth"
			@sort-data="options => $emit('sort-data', options)"
		/>

		<VBody
			:headerHeight="headerHeight"
			:headerCellWidth="headerCellWidth"
			:data="data"
			:detail="detail"
		/>
		
	</div>
</template>

<script>
import { bus } from './main'
import VHeader from './components/header/v-header.vue'
import VBody from './components/body/v-body.vue'

export default {
	name: 'VTable',
	components: {
		VHeader,
		VBody,
	},
	props: {
		width: {
			type: [String, Number],
			default: 1104
		},
		minWidth: {
			type: [String, Number],
			defaut: 0
		},
		height: {
			type: [String, Number],
			default: 883
		},
		minHeight: {
			type: [String, Number],
			defaut: 0
		},
		data: {
			type: Object,
			defautl: () => ({
				h: [],
				b: [],
				bf: [],
				f: null
			})
		},
		detail: {
			type: Boolean,
			default: true
		}
	},
	data: () => ({
		headerHeight: 0,
		headerCellWidth: [],
		sortOptions: {}
	}),
	computed: {
		setStyleTableContainer() {
			return {
				maxWidth: this.getStyleValue(this.width),
				minWidth: this.getStyleValue(this.minWidth),
				maxHeight: this.getStyleValue(this.height),
				minHeight: this.getStyleValue(this.minHeight),
			}
		},
		getHeader() {
			return this.data.h.length
				? this.data.h
				: null
		},
		getFooterType() {
			return this.data.f.type
		},
	},
	methods: {
		getStyleValue(value) {
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
		getHeaderHeight(value) {
			this.headerHeight = value
		},
		getHeaderCellWidth(array) {
			this.headerCellWidth = array
		}
	},
	created() {
		this.data.b = this.data.bf = [...this.data.b.map((curr, i) => ({ id: i + 1, ...curr  }))]
		bus.$on('filter-data', options => this.$emit('filter-data', options))
	}
}
</script>

<style lang="scss">
	@import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600&display=swap');

	.v-table-container {
		width: 100%;
		height: 100%;
		border: 2px solid #f6f6fb;
		border-radius: 12px;

		& * {
			font-family: 'Inter', sans-serif;
		}
	}
</style>