# izi-billing-table

```js
npm i -S @inowave/izi-billing-table@git+'link'
```
## Config:
| Name 				  	| Type 					   | Default 											| Description
| ---- 				  	| :--: 					   | :-----: 											| ---------------
| width 					| `[String, Number]`		| **auto** 											| Ширина таблицы
| minWidth 				| `[String, Number]`		| **0** 		   									| Минимальная ширина таблицы
| height 			  	| `[String, Number]`		| **auto** 											| Высота таблицы
| sticky 			  	| `Object`					| **{ pagination: null, header: null }** 	| Возможность прилипанию к верху pagination и header, передавать число 
| scrollable 			| `Boolean`					| **false** 										| Включить скролл в таблице и вычисляемую высоту
| minRows 			  	| `Number`					| **1** 												| Минимальное выводимое колличество рядов
| heightRows 			| `Number`					| **85** 											| Резервная высота для рядов таблицы
| data 					| `Object`					| **{ h:[], b:[], bf: [] }** 		   		| Данные (h: данные header, b: данные body, bf: данные для сортировки и фильтрации)
| search 				| `Object`					| **{ value: '', related: null }** 		   | Возможность передать внешнее значение для поиска related принимает String | Array 
| isClearFilter 		| `Boolean`					| **false**										 	| Скрыть все фильтры
| isPagination 		| `Boolean`					| **false**										 	| Показать пагинацию
| detail 				| `Boolean`					| **false** 		   							| Включить вывод детальных полей
| vLine 					| `Boolean`					| **false** 		   							| Включить вертикальные линии для ячеек
| paging 				| `Object`					| **{ page: 0, size: 20, results: 0 }** 	| Данные для пагинации
| bodyLabel 			| `String`					| **Cовпадений не найдено** 					| Вывод строки при отсутрствии данных

## Events:
```js
// @sort-date Возвращает { type, related, sorted }
{
  type: 'Тип сортировки',
  related: 'Имя свойства',
  sorted: 'Boolean(Возрастание/Убывание)'
}

// @filter-data Возвращает { related, value }
{
  related: 'Имя свойства',
  value: 'Выбранное значение',
}

/**
 * @handler Возвращает переданное значение для поля
 * работает только для переденных компнонетов {"component":{"name":"v-rate","value":"123","props":{"rate":0},"handler": 14081}}
 */
```

### Example template
```html
<VBillingTable
  width="1400"
  minWidth="500"
  :data="data"
  :detail="false"
  @sort-data="sortData"
  @filter-data="filterData" 
/>

<!-- Высота таблицы расчитывается автоматически, если нужна фиксированая высота передавайте :height="value' -->
```

### Example header data
```js
data: {
 h: [
	 {
		related: 'propery_name', // Связь с объектами body
		width: 200, // Первоночальная ширина столбца (для header-cell и body-cell)
		minWidth: 100, // Минимальная ширина столбца
		side: 'flex-left', // Позиционирование данных в ячейки (flex-left, center, flex-end)
		lineBreak: Boolean, // Смещать слова на следующую строку
		title: 'header_title_name', // Название поля
		hidden: true, // Скрыть колонку
		filter: true, // Включить фильтр
		filterType: 'search' | 'date_range', // Тип фильтра
		filterUsing: 'global' | 'local' // Выбор использования обработчиков фильтрации
		sort: true, // Включить сортировку
		sortType: 'string' | 'date' // Тип сортировки
		sortUsing: 'global' | 'local' // Выбор использования обработчиков сортировки

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
		filterType: 'search',
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
		filterType: 'search',
	},
	{
		related: 'start',
		width: 220,
		side: 'center',
		title: 'Дата активации',
		filter: true,
		filterType: 'date_range',
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
		filterType: 'date_range',
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
		filterType: 'search',
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
				<!-- name: есть внутренние шаболы ['v-action', 'v-active', 'v-date', v-amount', 'v-detail', 'v-status', 'v-tariff'] которые можно использовать передав строку с названием name: 'v-name' или можно предать свой компонент name: VSelfComponent пример реализации и передачи параметров можно посмотреть здесь table/src/components/body-templates данные нужно передавать в том же виде как и там. -->
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
		return this.h.find(h => h.related === rel)?.filterType ?? 'search'
	}

	const filterValue = (fo, method, curr) => {
		return Object.entries(fo)[method](c_fo => {
			const [related_name, value] = c_fo

			switch (defineFilterName(related_name)) {

				case 'search': {
					return defineValue(curr, related_name).includes(String(value))
				}

				case 'date_range': {
					const [start, end] = value
					const value_cell = new Date(new Date(defineValue(curr, related_name)).toDateString())

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
