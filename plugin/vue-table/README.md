# izi-billing-table

```js
npm i -S @inowave/izi-billing-table@git+
```
## Config:
| Name 				  	| Type 					   | Default 									| Description
| ---- 				  	| :--: 					   | :-----: 									| ---------------
| width 					| `[String, Number]`		| **1104** 									| Ширина таблицы
| minWidth 				| `[String, Number]`		| **0** 		   							| Минимальная ширина таблицы
| height 			  	| `[String, Number]`		| **883** 									| Высота таблицы
| minHeight 			| `[String, Number]`		| **0** 		   							| Минимальная высота таблицы
| data 					| `Object`					| **{ h:[], b:[], bg: [] }** 		   | Данные (h: данные header, b: данные body, bf: данные для сортировки и фильтрации)
| detail 				| `Boolean`					| **false** 		   					| Включить вывод детальных полей

## Events:
```js
// @sort-date Возвращает { type, related, sorder }
{
  type: 'Тип фильтра',
  related: 'Имя свойства',
  sorted: 'Boolean(Возрастание/Убывание)'
}

// @filter-data Возвращает { related, value, component }
{
  related: 'Имя свойства',
  value: 'Выбранное значение',
  component: 'Объект свойств компонента'
}
```

### Example template
```html
<VBillingTable
  width="1400"
  height="700px"
  minWidth="500"
  :data="data"
  :detail="false"
  @sort-data="sortData"
  @filter-data="filterData"
/>
```

### Example header data
```js
data: {
 h: [
	 {
		related: 'propery_name', // Связь с объектами body
		width: 200, // Ширина столбца
		side: 'flex-left', // Позиционирование данных в ячейки (flex-left, center, flex-end)
		title: 'header_title_name', // Название поля
		filter: true, // Включить фильтр
		sort: true, // Включить сортировку

		// В случае, если нужен какой-либо компонент:
		component: {
			/**
			 * name: 'Name' (Название компонента)
			 * при передачи строки типа 'select' загружается внутренний компонет
			 * при передачи названия компонента типа VInput - загружается ваш компонет (импортированный и зарегестрированный)
			 * спасок внутренних (select, input, date)
			 * для создания создания/передачи своего комонента можно посмотреть пример тут table/src/components/header-templates
			 */
			name: 'select', // Название компонента
			value: null, // Первичное/изменяемое значение
			props: {} // Любые свойства, с которыми работает внутренний или внешний компонент
		},
	},
	{
		related: 'unit',
		width: 200,
		side: 'flex-left',
		title: 'Филиал',
		filter: true,
		component: {
			name: 'select',
			value: null,
		},
	},
	{
		related: 'service_main',
		width: 130,
		side: 'flex-left',
		title: 'Текущий тариф',
		sort: true,
		sortType: 'string',
		filter: true,
	},
	{
		related: 'start',
		width: 220,
		side: 'center',
		title: 'Дата активации',
		filter: true,
		sort: true,
		sortType: 'date',
		component: {
			name: 'date',
			value: [],
		},
	},
	{
		related: 'end',
		width: 220,
		side: 'center',
		title: 'Дата окончания',
		filter: true,
		sort: true,
		sortType: 'date',
		component: {
			name: 'date',
			value: [],
		},
	},
	{
		related: 'status',
		width: 180,
		side: 'flex-start',
		title: 'Статус',
		filter: true,
		component: {
			name: 'select',
			value: null,
		}
	},
	{
		related: 'action',
		width: 100,
		side: 'center',
		title: 'Продлить'
	},
],
}
```

### Example created body 
```html
Пример готового объекта данных
b: [
	<!-- Так -->
	{
		property_name: 'property_value'
	}
	<!-- Либо так -->
	{
		property_name: {
			component: {
				<!-- name: есть внутренние шаболы ['v-action', 'v-active', 'v-date', 'v-detail', 'v-status', 'v-tariff'] которые можно использовать передав строку с названием name: 'v-active' или можно предать свой компонент name: VSelfComponent пример реализации и передачи параметров можно посмотреть здесь table/src/components/body-templates данные нужно передавать в том же виде как и там. -->
				name: "componentName" || componentName,
				<!-- value: значение ячейки или выбранное/изменяемое значение -->
				value: value 
				<!-- props: любые свойства, который поддерживает компонент -->
				props: {}
			}
		}
	},
]
<!-- Если у таблицы свойство detail: true передаем в объкт данныех объект detail-->
b: [
	{
		property_name_1: 'name1',
		property_name_2: {
			component: {
				name: 'component',
				value: value,
				props: {}
			}
		},
		....
		detail: {
			component: {
				name: VSelfDetail,
				props: {
					title: 'Текст который будет выводиться',
				}
			},
		}
	},
	...
]
```

```js
// Реализованный пример генерации данных для body
getBodyServiceTable(units, services) {
	const getServiceMain = unit_service => {
		const { id: s_id } = unit_service
		const service = services.find(s => s.id === s_id)
		
		if (service) {
			const {
				price,
				name: per,
				currency: { postfix }
			} = service.options[0] // HARDCODE
			
			return {
				...unit_service, per, price, postfix
			}
		} else {
			return { ...unit_service, price: null }
		}
	}
	const getStatusColor = status_id => {
		const LOCAL_STATUS_COLOR = {
			0: '#ec4848',
			1: '#4fd1c5',
			2: '#fff',
			4: '#a2a2b9',                                         
			5: '#fff',
			6: '#7f9cf5'
		}

		return LOCAL_STATUS_COLOR[status_id]
	}
	const generateComponent = data => {
		const { name, value, props } = data

		return {
			component: { name, value, props }
		}
	}

	return units.map((unit, i) => {
		const {
			name, 
			status,
			service_main,
			dt_action_main: [start, end],
		} = unit
		const { name: value } = getServiceMain(service_main)

		return {
			id: i + 1,
			unit: name,
			service_main: generateComponent({
				name: 'v-tariff',
				value,
				props: getServiceMain(service_main)
			}),
			start: generateComponent({
				name: 'v-date',
				value: start,
			}),
			end: generateComponent({
				name: 'v-date',
				value: end,
			}),
			status: generateComponent({
				name: 'v-status',
				value: status.name,
				props: { color: getStatusColor(status.id) }
			}),
			active: generateComponent({
				name: 'v-action',
				value: 'Продлить',
				props: { type: 'link', path: '/billing/tariff' }
			})
		}
	})
}
```

### Example filter

```js
// Данные выводятся из массива data.bf, для фильтрации нужно переопределать его или фильтровать с сервера

filterData({ related, value, component }) {
	this.$set(this.filterOptions, related, { value, component })

	if (this.filterOptions[related] && (!value || !value.length)) {
		this.$delete(this.filterOptions, related)
	}

	this.data.bf = this.data.b.filter(curr => {
		const rowFilter = Object.entries(curr).reduce((acc, fc) => {
			const [key, value] = fc
			
			if (Object.keys(this.filterOptions).includes(key)) {
				acc[key] = typeof value === 'object'
					? value.component.value
					: value
			}

			return acc

		}, {})

		return Object.entries(this.filterOptions).every(fo => {
			const [key, { value, component }] = fo

			switch (component?.name) {
				case 'input': return curr[key].includes(value)
				case 'date': {
					const currDate = new Date(curr[key].component.value)
					const [start, end] = value

					return currDate >= new Date(start) && currDate <= new Date(end) && value.length
				}
				default: {
					return Object.values(rowFilter).includes(value)
				}
			}

		})
	})
},
```

### Example sort

```js
// Переопределяем данные массива data.bf для сортировки

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
```
