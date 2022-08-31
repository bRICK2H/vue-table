<template>
	<div v-show="!hData.hidden"
		class="v-table-header-cell"
		:ref="headerCellRef"
		:style="{
			minWidth: `${hData.minWidth !== undefined ? hData.minWidth : 0}px`,
			width: `${hData.width}px`,
			justifyContent: `${hData.side}`,
			whiteSpace: hData.lineBreak ? 'pre-line' : 'nowrap'
		}"
	>
		<!-- SORT -->
		<span v-show="hData.sort"
			class="v-table-header-cell-sort"
		>
			<span class="v-table-header-cell-sort--up"
				:class="{ 'v-table-header-cell-sort--active-up': hData.related === sortOptions.related && sortOptions.sorted }"
				title="Возрастание"
				@click="$emit('handler-sort', {
					use: hData.sortUsing,
					type: hData.sortType,
					related: hData.related,
					sorted: true
				})"
			></span>
			<span class="v-table-header-cell-sort--down"
				:class="{ 'v-table-header-cell-sort--active-down': hData.related === sortOptions.related && !sortOptions.sorted }"
				title="Убывание"
				@click="$emit('handler-sort', {
					use: hData.sortUsing,
					type: hData.sortType,
					related: hData.related,
					sorted: false
				})"
			></span>
		</span>

		<!-- TEMPLATE -->

		<!-- SELF -->
		<div v-if="component && typeof component.name === 'object'">
			<component :is="component.name" v-bind="hData"/>
		</div>

		<!-- SELECT -->
		<template v-else-if="component && component.name === 'select' && isFilter">
			<VSelect
				:width="hData.width - 40"
				:placeholder="hData.title"
				:raisePlaceholder="true"
				:clearable="false"
				:showSelectedArrow="false"
				:outsideOpen="isElement"
				v-model="component.value"
				v-bind="{
					...component.props,
					options: component.props && component.props.options
						? component.props.options
						: select.options
				}"
			/>
		</template>

		<!-- INPUT -->
		<template v-else-if="component && component.name === 'input' && isFilter">
			<VInput
				v-model="component.value"
				:placeholder="hData.title"
				:raisePlaceholder="isElement"
				:outsideFocus="isElement"
			/>
		</template>

		<!-- Date -->
		<template v-else-if="component && component.name === 'date' && isFilter">
			<VDatePicker
				:placeholder="hData.title"
				:raisePlaceholder="isElement"
				:outsideFocus="isElement"
				v-bind="{ component }"
			/>
		</template>

		<!-- PRIMITIVE -->
		<template v-else> 
			<span class="v-table-header-cell-title">
				{{ hData.title }}
			</span>
		</template>

		<!-- FILTER -->
		<span v-if="hData.filter && component && (!component.value || !component.value.length)"
			tabindex="0"
			class="v-table-header-cell-filter"
			:class="{'v-table-header-cell-filter--active': isFilter}"
			@click="toggleFilter"
		></span>

		<!-- CLEAR -->
		<template v-if="component && ((!Array.isArray(component.value) && component.value) || (Array.isArray(component.value) && component.value.length))">
			<span class="v-table-header-cell-clear-select"
				@click="clearFilter"
			></span>
		</template>

	</div>
</template>

<script>
import { bus } from '../../main'
import VSelect from '@inowave/izi-select'
import VInput from '../header-templates/v-input.vue'
import VDatePicker from '../header-templates/v-datepicker.vue'

export default {
	name: 'VHeaderCell',
	components: {
		VSelect,
		VInput,
		VDatePicker
	},
	props: [
		'body',
		'hData',
		'sortOptions',
		'transformRef',
		'filterOptions',
		'isClearFilter'
	],
	data: () => ({
		isFilter: false,
		isElement: false,
		headerCellRef: '',
		select: {
			options: []
		},
	}),
	computed: {
		component() {
			return this.hData.component && this.hData.component.name
				? this.hData.component
				: null
		},
	},
	methods: {
		toggleFilter() {
			this.isElement = true
			this.isFilter = !this.isFilter

			if (this.component) {
				this.$emit('toggle-filter', { component: this.component.name })
				
				if (this.component.name === 'date') {
					this.component.value = []
				}
			}

			this.$delete(this.sortOptions, 'index')
			this.$delete(this.sortOptions, 'sorted')
		},
		clearFilter(value) {
			if (Array.isArray(this.component.value)) {
				this.component.value = []
			} else {
				this.component.value = null
			}

			this.isFilter = false
		},
		getLocalSelectOptions(name) {
			if (this.component?.name === name) {
				const related = this.hData.related

				return [...new Set(this.body.map(mc => {
					return typeof mc[related] === 'object'
						? this.component?.props?.options && this.component?.props?.label
							? mc[related][this.component.props.label]
							: mc[related].component.value
						: mc[related]
				}))].filter(fe => fe !== undefined && Boolean(fe))

			} else {
				return []
			}
		},
		getDates() {	
			const d = new Date()
			return [
				new Date(d.getFullYear(), d.getMonth(), d.getDate()),
				new Date(d.getFullYear(), d.getMonth(), d.getDate() + 7)
			]
		},
		calculateCellWidth() {
			const computedWidth = this.$refs[this.headerCellRef]?.offsetWidth
			this.$emit('get-auto-header-cell-width', {
				[this.hData.related]: !computedWidth ? this.hData.width : computedWidth
			})
		}
	},
	watch: {
		filterOptions: {
			immediate: true,
			deep: true,
			handler(options) {
				if (!Object.keys(options).length) {
					this.isFilter = Array.isArray(this.component?.value)
						? !!this.component?.value.length
						: !!this.component?.value
				} else {
					const { related } = options
					
					if (related !== this.hData.related && this.component && (!this.component.value || !this.component.value.length)) {
						this.isFilter = false
					}
				}
			}
		},
		'hData.component.value': {
			deep: true,
			handler(value) {
				bus.$emit('handler-filter', {
					value,
					use: this.hData.filterUsing,
					related: this.hData.related,
				})
			}
		},
		body: {
			deep: true,
			immediate: true,
			handler() {
				this.select.options = this.getLocalSelectOptions('select')
			}
		},
		hData: {
			deep: true,
			immediate: true,
			handler() {
				this.select.options = this.getLocalSelectOptions('select')
			}
		},
		isClearFilter() {
			this.isFilter = false
		}
	},
	created() {
		this.headerCellRef = this.transformRef('header-cell')
	},
	mounted() {
		this.calculateCellWidth()
		window.addEventListener('resize', this.calculateCellWidth)
	}
}
</script>

<style lang="scss">
	.v-table-header-cell {
		display: flex;
		align-items: center;
		position: relative;

		&:not(:last-of-type) {
			margin: auto 35px auto 0;
		}
		&:last-of-type {
			margin: auto 0 auto auto;
		}
	}

	.v-table-header-cell-title {
		font-size: 13px;
		font-weight: 600;
		color: #a2a2b9;
		overflow: hidden;
		text-overflow: ellipsis;
	}

	.v-table-header-cell-sort,
	.v-table-header-cell-filter {
		cursor: pointer;
	}
	.v-table-header-cell-sort {
		width: 12px;
		height: 24px;
		margin-right: 10px;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		align-items: center;

		&--up,
		&--down {
			width: 11px;
			height: 10px;
			background: url('../../assets/img/png/down.png') no-repeat center / cover;
			transition: .2s;
			opacity: .5;

			&:hover {
				opacity: 1;
			}
		}
		&--up {
			transform: rotate(180deg);

			&:hover {
				transform: rotate(180deg) scale(1.2);
			}
		}
		&--down {
			&:hover {
				transform: scale(1.2)
			}
		}
		
		&--active-up,
		&--active-down {
			opacity: .8;
		}
		&--active-up {
			transform: rotate(180deg) scale(1.2);
		}
		&--active-down {
			transform: scale(1.2);
		}
	}
	.v-table-header-cell-filter {
		width: 15px;
		min-width: 15px;
		height: 14px;
		margin-left: 10px;
		background: url('../../assets/img/png/filter.png') no-repeat center / contain;

		&--active {
			transform: scale(1.2);
			filter: brightness(.8);
		}

		&:hover {
			filter: brightness(.5)
		}
	}
	.v-table-header-cell-clear-select {
		min-width: 15px;
		min-height: 15px;
		margin-left: 10px;
		cursor: pointer;
		background: url('../../assets/img/png/close.png') no-repeat center / cover;
		transition: .2s;

		&:hover {
			filter: brightness(.5)
		}
	}
</style>