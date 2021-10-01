<template>
	<div>
		<div style="width:100%;height:100vh;display:flex;justify-content:flex-start;align-items:center;">
			<VTable
				:width="2050"
				height="700px"
				minWidth="500"
				:data="data"
				:detail="true"
				@sort-data="sortData"
				@filter-data="filterData"
			/>
		</div>
	</div>
</template>

<script>
import VTable from '../plugin/vue-table/src/main'
import VSelfStatus from './self/v-self-status.vue'
import VSelfAction from './self/v-self-action.vue'
import VSelfActive from './self/v-self-active.vue'
import VSelfDetail from './self/v-self-detail.vue'

	const getDates = () => {	
		const d = new Date()
		return [
			new Date(d.getFullYear(), d.getMonth(), d.getDate()),
			new Date(d.getFullYear(), d.getMonth(), d.getDate() + 7)
		]
	}

export default {
	name: 'App',
	components: {
		VTable,
		VSelfStatus
	},
	data: () => ({
		filterOptions: {},
		data: {
			h: [
					{
						type: 'payment',
						width: 260,
						side: 'flex-start',
						title: 'Тип операции / Способ оплаты',
						filter: true,
						component: {
							name: 'select',
							value: null,
							props: {
								label: 'operation',
								reduce: o => o.operation
							}
						},
					},
					{
						type: 'invoice',
						width: 125,
						side: 'center',
						title: 'Счет',
						filter: true,
						component: {
							name: 'input',
							value: null,
						},
					},
					{
						type: 'date',
						width: 190,
						side: 'center',
						title: 'Дата платежа',
						filter: true,
						sort: true,
						component: {
							name: 'date',
							value: getDates(),
							// value: [],
							// value: null
						},
					},
					{
						type: 'traffic',
						width: 185,
						side: 'center',
						title: 'Движение ДС',
						filter: true,
						component: {
							name: 'select',
							value: null,
						},
					},
					{
						type: 'branch',
						width: 230,
						side: 'center',
						title: 'Филиал',
						filter: true,
						component: {
							name: 'select',
							value: null,
						},
					},
					{
						type: 'sum',
						width: 100,
						side: 'center',
						title: 'Сумма',
						sort: true,
						filter: true,
					},
					{
						type: 'file',
						width: 100,
						side: 'center',
						title: 'Чек'
					},
					{
						type: 'self',
						width: 130,
						side: 'flex-start',
						title: 'Статус'
					},
					{
						type: 'self',
						width: 100,
						side: 'flex-start',
						title: 'Активность',
						component: {
							name: VSelfActive,
							value: false,
							props: {
								label: 'Активность',
							}
						}
					},
					{
						type: 'self',
						width: 180,
						side: 'flex-start',
						title: 'Действия'
					},
			],
			b: [
				{
					payment: {
						operation: 'Возврат',
						type_payment: 'Банковский перевод'
					},
					invoice: 'ЛС',
					date: '30.10.2021',
					traffic: 'Поступление',
					branch: 'Подразделение 1',
					sum: {
						amount: 50000,
						currency: '₽'
					},
					check: {
						title: 'Скачать .pdf',
						path: '/test.pdf'
					},
					self_status: {
						component: {
							name: VSelfStatus,
							props: {
								title: 'В обработке',
								color: '#F7D631'
							}
						}
					},
					self_active: {
						component: {
							name: VSelfActive,
							value: false,
							props: {
								viewType: "rectangle"
							}
						}
					},
					self_action: {
						component: {
							name: VSelfAction,
							props: {
								title: 'Загрузить документ',
							}
						},
					},
					detail: {
						component: {
							name: VSelfDetail,
							props: {
								title: '1: Оплата услуг «Название услуги 1» (100 ₽), «Название услуги 2» (100 ₽), «Название услуги 3» (100 ₽), «Название услуги 4» (100 ₽)',
							}
						},
					}
				},
				{
					payment: {
						operation: 'Оплата за тариф',
						type_payment: 'Банковская карта'
					},
					invoice: 'БС',
					date: '02.10.2021',
					traffic: 'Поступление',
					branch: 'Подразделение 2',
					sum: {
						amount: -500,
						currency: 'Б'
					},
					check: {
						title: 'Скачать .pdf',
						path: '/test.pdf'
					},
					self_status: {
						component: {
							name: VSelfStatus,
							props: {
								title: 'Ошибка',
								color: '#EC4848'
							}
						},
					},
					self_active: {
						component: {
							name: VSelfActive,
							value: true,
							props: {
								viewType: "rectangle"
							}
						}
					},
					self_action: {
						component: {
							name: VSelfAction,
							props: {
								title: 'Загрузить документ',
							}
						},
					},
					detail: {
						component: {
							name: VSelfDetail,
							props: {
								title: '2: Оплата услуг «Название услуги 1» (100 ₽), «Название услуги 2» (100 ₽), «Название услуги 3» (100 ₽), «Название услуги 4» (100 ₽)',
							}
						},
					}
				},
				{
					payment: {
						operation: 'Оплата услугой',
						type_payment: 'Банковская карта'
					},
					invoice: 'ЛС',
					date: '6.10.2021',
					traffic: 'Списание',
					branch: 'Подразделение 1',
					sum: {
						amount: -1000,
						currency: '₽'
					},
					check: {
						title: 'Скачать .pdf',
						path: '/test.pdf'
					},
					self_status: {
						component: {
							name: VSelfStatus,
							props: {
								title: 'Готов',
								color: '#4FD1C5'
							}
						},
					},
					self_active: {
						component: {
							name: VSelfActive,
							value: false,
							props: {
								viewType: "rectangle"
							}
						}
					},
					self_action: {
						component: {
							name: VSelfAction,
							props: {
								title: 'Загрузить документ',
							}
						},
					},
					detail: {
						component: {
							name: VSelfDetail,
							props: {
								title: '3: Оплата услуг «Название услуги 1» (100 ₽), «Название услуги 2» (100 ₽), «Название услуги 3» (100 ₽), «Название услуги 4» (100 ₽)',
							}
						},
					}
				},
				{
					payment: {
						operation: 'Оплата за тариф',
						type_payment: 'Банковская карта'
					},
					invoice: 'ЛС',
					date: '20.10.2021',
					traffic: 'Списание',
					branch: 'Подразделение 3',
					sum: {
						amount: -50000,
						currency: '₽'
					},
					check: {
						title: 'Скачать .pdf',
						path: '/test.pdf'
					},
					self_status: {
						component: {
							name: VSelfStatus,
							props: {
								title: 'Новый',
								color: '#7F9CF5'
							}
						},
					},
					self_active: {
						component: {
							name: VSelfActive,
							value: false,
							props: {
								viewType: "rectangle"
							}
						}
					},
					self_action: {
						component: {
							name: VSelfAction,
							props: {
								title: 'Загрузить документ',
							}
						},
					},
					detail: {
						component: {
							name: VSelfDetail,
							props: {
								title: '4: Оплата услуг «Название услуги 1» (100 ₽), «Название услуги 2» (100 ₽), «Название услуги 3» (100 ₽), «Название услуги 4» (100 ₽)',
							}
						},
					}
				},
				{
					payment: {
						operation: 'Пополнение БС',
					},
					invoice: 'БС',
					date: '29.09.2021',
					traffic: 'Пополнение',
					branch: 'Подразделение 1',
					sum: {
						amount: 500,
						currency: 'Б'
					},
					check: {
						title: 'Скачать .pdf',
						path: '/test.pdf'
					},
					self_status: {
						component: {
							name: VSelfStatus,
							props: {
								title: 'Готов',
								color: '#4FD1C5'
							}
						},
					},
					self_active: {
						component: {
							name: VSelfActive,
							value: false,
							props: {
								viewType: "rectangle"
							}
						}
					},
					self_action: {
						component: {
							name: VSelfAction,
							props: {
								title: 'Загрузить документ',
							}
						},
					},
					detail: {
						component: {
							name: VSelfDetail,
							props: {
								title: '5: Оплата услуг «Название услуги 1» (100 ₽), «Название услуги 2» (100 ₽), «Название услуги 3» (100 ₽), «Название услуги 4» (100 ₽)',
							}
						},
					}
				},
				{
					payment: {
						operation: 'Оплата услугой',
						type_payment: 'Банковская карта'
					},
					invoice: 'ЛС',
					date: '11.10.2021',
					traffic: 'Списание',
					branch: 'Подразделение 2',
					sum: {
						amount: -1000,
						currency: '₽'
					},
					check: {
						title: 'Скачать .pdf',
						path: '/test.pdf'
					},
					self_status: {
						component: {
							name: VSelfStatus,
							props: {
								title: 'Готов',
								color: '#4FD1C5'
							}
						},
					},
					self_active: {
						component: {
							name: VSelfActive,
							value: false,
							props: {
								viewType: "rectangle"
							}
						}
					},
					self_action: {
						component: {
							name: VSelfAction,
							props: {
								title: 'Загрузить документ',
							}
						},
					},
					detail: {
						component: {
							name: VSelfDetail,
							props: {
								title: '6: Оплата услуг «Название услуги 1» (100 ₽), «Название услуги 2» (100 ₽), «Название услуги 3» (100 ₽), «Название услуги 4» (100 ₽)',
							}
						},
					}
				},
				{
					payment: {
						operation: 'Оплата услугой',
						type_payment: 'Банковская карта'
					},
					invoice: 'ЛС',
					date: '6.10.2021',
					traffic: 'Списание',
					branch: 'Подразделение 1',
					sum: {
						amount: -500,
						currency: '₽'
					},
					check: {
						title: 'Скачать .pdf',
						path: '/test.pdf'
					},
					self_status: {
						component: {
							name: VSelfStatus,
							props: {
								title: 'Готов',
								color: '#4FD1C5'
							}
						},
					},
					self_active: {
						component: {
							name: VSelfActive,
							value: false,
							props: {
								viewType: "rectangle"
							}
						}
					},
					self_action: {
						component: {
							name: VSelfAction,
							props: {
								title: 'Загрузить документ',
							}
						},
					},
					detail: {
						component: {
							name: VSelfDetail,
							props: {
								title: '7: Оплата услуг «Название услуги 1» (100 ₽), «Название услуги 2» (100 ₽), «Название услуги 3» (100 ₽), «Название услуги 4» (100 ₽)',
							}
						},
					}
				},
				{
					payment: {
						operation: 'Пополнение БС',
					},
					invoice: 'ЛС',
					date: '10.10.2021',
					traffic: 'Пополнение',
					branch: 'Подразделение 4',
					sum: {
						amount: 500,
						currency: 'Б'
					},
					check: {
						title: 'Скачать .pdf',
						path: '/test.pdf'
					},
					self_status: {
						component: {
							name: VSelfStatus,
							props: {
								title: 'Готов',
								color: '#4FD1C5'
							}
						},
					},
					self_active: {
						component: {
							name: VSelfActive,
							value: true,
							props: {
								viewType: "rectangle"
							}
						}
					},
					self_action: {
						component: {
							name: VSelfAction,
							props: {
								title: 'Загрузить документ',
							}
						},
					},
					detail: {
						component: {
							name: VSelfDetail,
							props: {
								title: '8: Оплата услуг «Название услуги 1» (100 ₽), «Название услуги 2» (100 ₽), «Название услуги 3» (100 ₽), «Название услуги 4» (100 ₽)',
							}
						},
					}
				},
				{
					payment: {
						operation: 'Оплата услугой',
						type_payment: 'Банковская карта'
					},
					invoice: 'ЛС',
					date: '5.10.2021',
					traffic: 'Списание',
					branch: 'Подразделение 1',
					sum: {
						amount: -550,
						currency: '₽'
					},
					check: {
						title: 'Скачать .pdf',
						path: '/test.pdf'
					},
					self_status: {
						component: {
							name: VSelfStatus,
							props: {
								title: 'Готов',
								color: '#4FD1C5'
							}
						},
					},
					self_active: {
						component: {
							name: VSelfActive,
							value: true,
							props: {
								viewType: "rectangle"
							}
						}
					},
					self_action: {
						component: {
							name: VSelfAction,
							props: {
								title: 'Загрузить документ',
							}
						},
					},
					detail: {
						component: {
							name: VSelfDetail,
							props: {
								title: '9: Оплата услуг «Название услуги 1» (100 ₽), «Название услуги 2» (100 ₽), «Название услуги 3» (100 ₽), «Название услуги 4» (100 ₽)',
							}
						},
					}
				},
				{
					payment: {
						operation: 'Оплата услугой',
						type_payment: 'Банковская карта'
					},
					invoice: 'ЛС',
					date: '4.10.2021',
					traffic: 'Списание',
					branch: 'Подразделение 1',
					sum: {
						amount: -800,
						currency: '₽'
					},
					check: {
						title: 'Скачать .pdf',
						path: '/test.pdf'
					},
					self_status: {
						component: {
							name: VSelfStatus,
							props: {
								title: 'Готов',
								color: '#4FD1C5'
							}
						},
					},
					self_active: {
						component: {
							name: VSelfActive,
							value: false,
							props: {
								viewType: "rectangle"
							}
						}
					},
					self_action: {
						component: {
							name: VSelfAction,
							props: {
								title: 'Загрузить документ',
							}
						},
					},
					detail: {
						component: {
							name: VSelfDetail,
							props: {
								title: '10: Оплата услуг «Название услуги 1» (100 ₽), «Название услуги 2» (100 ₽), «Название услуги 3» (100 ₽), «Название услуги 4» (100 ₽)',
							}
						},
					}
				},
			],
			bf: [],
			f: {
				// type: 'scroll'
			}
		}
	}),
	methods: {
		filterData(options) {
			const { type, value, component, index } = options

			this.$set(this.filterOptions, type, { value, component })
			if (this.filterOptions[type] && (!value || !value.length)) {
				this.$delete(this.filterOptions, type)
			}

			this.data.bf = this.data.b.filter(curr => {
				const rowFilter = Object.entries(curr).reduce((acc, fc) => {
					const [key, value] = fc
					
					if (Object.keys(this.filterOptions).includes(key)) {

						if (typeof value === 'object') {
							if (this.filterOptions[key].component?.props?.label) {
								acc[key] = value[this.filterOptions[key].component.props.label]
							}
						} else {
							acc[key] = value
						}

					}

					return acc

				}, {})

				return Object.entries(this.filterOptions).every(fo => {
					const [key, { value, component }] = fo

					switch (component?.name) {
						case 'input': return curr[key].includes(value)
						case 'date': {
							const currDate = new Date(curr[key].split('.').reverse().join('.'))
							const [start, end] = value
							return currDate >= start && currDate <= end && value.length
						}
						default: return Object.values(rowFilter).includes(value)
					}

				})
			})
		},
		sortData({ type, sorted, index }) {

			switch (type) {
				case 'sum': {
					this.data.bf.sort(({ [type]: { amount: a } }, { [type]: { amount: b } }) => {
						return sorted ? b - a : a - b
					})
				}
					break

				case 'date': {
					this.data.bf.sort(({ [type]: a }, { [type]: b }) => {
						const ma = new Date(a.split('.').reverse().join('.'))
						const mb = new Date(b.split('.').reverse().join('.'))
						
						return sorted ? mb - ma : ma - mb
					})
				}

					break
			}
			
		},
	},
	watch: {
		// 'data.h': {
		// 	deep: true,
		// 	handler(val) {
		// 		const v = val.find(c => c.type === 'self' && c.component && typeof c.component.name === 'object')
		// 		this.data.b.forEach(curr => {
		// 			curr['self_active'].component.value = v.component.value
		// 		}) 
		// 	}
		// }
	}
}
</script>

<style lang="scss">
	* {
		margin: 0;
		padding: 0;
		box-sizing: border-box;
	}
</style>