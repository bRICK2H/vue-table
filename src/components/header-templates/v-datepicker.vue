<template>
	<div class="v-datepicker-container">
		<VDatePickerLocal
			ref="v-datepicker"
			:local="datelocal"
			:format="'DD.MM'"
			:value="component.value"
			:class="{ 'datepicker--empty': !component.value.length }"
			rangeSeparator="-"
			inputClass="form_element-datepicker"
			show-buttons
			popupClass="ch_datep-datepicker"
			v-model="currDate"
			@confirm="value => component.value = value"
			@cancel="currDate = component.value.length ? component.value : getDates()"
		/>

		<transition name="placeholder">
			<span v-show="isPlaceholder && raisePlaceholder || component.value.length"
				:style="{ maxWidth: width === 'auto' ? `calc(100% - 10px)` : `${width - 20}px` }"
				class="picker-title"
			>
				{{ placeholder }}
			</span>
		</transition>
	</div>
</template>

<script>
import VDatePickerLocal from 'vue-datepicker-local'

export default {
	name: 'VDatePicker',
	components: {
		VDatePickerLocal
	},
	props: {
		component: {
			type: Object,
			default: () => ({})
		},
		placeholder: {
			type: String,
			default: ''
		},
		raisePlaceholder: {
			type: Boolean,
			default: false
		},
		outsideFocus: {
			type: Boolean,
			default: false
		},
		width: {
			type: [Number, String],
			default: 'auto'
		},
	},
	data: () => ({
		currDate: [],
		isOpen: false,
		isPlaceholder: false,
		datelocal: {
			dow: 1,
			hourTip: 'Выбор часа', 
			minuteTip: 'Выбор минуты',
			secondTip: 'Выбор секунды',
			yearSuffix: '',
			cancelTip: 'Отменить',
			submitTip: 'Применить',
			months: ['Янв', 'Фев', 'Март', 'Апр', 'Май', 'Июнь', 'Июль', 'Авг', 'Сент', 'Окт', 'Ноя', 'Дек'],
			monthsHead: ['Январь', 'Февраль', 'Март', 'Апрель', 'Май', 'Июнь', 'Июль', 'Август', 'Сентябрь', 'Октябрь', 'Ноябрь', 'Декабрь'],
			weeks: ['Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб', 'Вс']
		},
	}),
	methods: {
		getDates() {	
			const d = new Date()
			return [
				new Date(d.getFullYear(), d.getMonth(), d.getDate()),
				new Date(d.getFullYear(), d.getMonth(), d.getDate() + 7)
			]
		},
	},
	watch: {
		outsideFocus: {
			immediate: true,
			async handler(isFocus) {
				await this.$nextTick()

				if (isFocus) {
					this.$refs['v-datepicker'].$el.click()
					this.$refs['v-datepicker'].$el.firstChild.focus()
					this.isPlaceholder = true
					this.isOpen = true
				}
			}
		},
		isOpen: {
			immediate: true,
			handler() {
				this.currDate = !this.component?.value.length
						? this.getDates()
						: this.component.value
			}
		}
	},
	mounted() {
		if (this.$refs['v-datepicker']) {
			this.$refs['v-datepicker'].$el.firstChild.addEventListener('blur', e => {
				this.isPlaceholder = false
				this.isOpen = false
			})
			this.$refs['v-datepicker'].$el.firstChild.addEventListener('focus', e => {
				this.isPlaceholder = true
				this.isOpen = true
			})
		}
	},
}
</script>

<style lang="scss">
.v-datepicker-container {
	position: relative;

	// input
.datepicker.datepicker-range {
	min-width: 100%;
}

// menu
.datepicker > input.focus {
	border-color: #cfcde5;
	box-shadow: none !important;
}
.datepicker > input {
	height: 48px;
	padding: 0 10px;
	border: 2px solid #eeedf7;
	border-radius: 8px;
	font-size: 14px;
	font-weight: 500;
	color: #1F1F33;
	text-align: center;

	&:focus {
		border: 2px solid #cfcde5;
	}
	&::placeholder {
		font-size: 14px;
		color: #b7b7cc;
		font-weight: 300;
	}
}
.datepicker {
	&--empty {
		input {
			font-size: 14px;
			color: #b7b7cc;
			font-weight: 300;
		}
	}
}
.datepicker-range .datepicker-popup.ch_datep-datepicker {
	justify-content: flex-end;
}

.header-date {
    position: relative;
    z-index: 1;
    background-color:#4bbac5;
    padding-left: 4px;
    padding-right: 4px;
    border-right: 1px solid #90d5db;
    min-height: 44px;
}
.datepicker-range .datepicker-popup.ch_datep-datepicker {
    width: 560px;
    display: flex;
    flex-wrap: wrap;
    margin-left: -2px;
    margin-top: 17px;
}

.ch_datep-datepicker .datepicker__buttons {
	display: flex;
	align-items: flex-end;
}
.ch_datep-datepicker .datepicker__buttons button {
	width: 156px;
	border-radius: 12px;
	border: none;
	padding: 13px 0;
	font-weight: 900;
	font-size: 16px;
	cursor: pointer;
	transition: .2s;
}
.ch_datep-datepicker .datepicker__buttons .datepicker__button-select{
	border: 2px solid #1F1F33;
	background-color: #1F1F33;
	color: #fff;
}

.ch_datep-datepicker .datepicker__buttons .datepicker__button-select:hover {
	background-color: rgba(31, 31, 51, .8);
}

.ch_datep-datepicker .datepicker__buttons .datepicker__button-cancel {
	border: 2px solid #F6F6FB;
	background-color: #fff;
	color: #1F1F33;
}

.ch_datep-datepicker .datepicker__buttons .datepicker__button-cancel:hover {
	border: 2px solid #e7e7ee;
	background-color: rgb(240, 240, 240);
}

// .ch_datep-datepicker .calendar-date.calendar-date-selected {
// 	background: #F6F6FB;
// 	position: relative;
// 	z-index: 10;
// }
.datepicker-popup.ch_datep-datepicker {
	.calendar {
		flex: 1 1 48%;
		
		.calendar-date.calendar-date-selected  {
			background: #F6F6FB;
			position: relative;
			z-index: 10;

			&:after {
				content: '';
				width: 100%;
				height: 100%;
				position: absolute;
				top: 0;
				left: 0;
				z-index: -1;
				background: #76768C;
			}
		}

		&:nth-child(1) {
			.calendar-date.calendar-date-selected {
				border-top-left-radius: 50%;
				border-bottom-left-radius: 50%;
				
				&:after {
					border-top-right-radius: 50%;
					border-bottom-right-radius: 50%;
				}
			}
		}
		&:nth-child(2) {
			.calendar-date.calendar-date-selected {
				border-top-right-radius: 50%;
				border-bottom-right-radius: 50%;
				
				&:after {
					border-top-left-radius: 50%;
					border-bottom-left-radius: 50%;
				}
			}
		}
	}
}
.calendar-date {
	width: 36px;
	height: 36px;
	display: flex;
	align-items: center;
	justify-content: center;
}
.calendar-body {
	width: auto;
	height: calc(100% - 35px);

	.calendar-week {	
		font-weight: 900;
		color: #A2A2B9;
	}
	.calendar-date {
		color: #1F1F33;
	}
	.calendar-date.calendar-date-selected {
		color: #fff;
	}
}
.datepicker-popup {
	border: none;
	border-radius: 12px;
	padding: 15px;
}
.calendar-body .calendar-days {
	height: 300px;
	display: flex;
	flex-wrap: wrap;
}
.ch_datep-datepicker.calendar-date:hover, .calendar-date-on {
	background: #F6F6FB;

	&:nth-child(7n + 7) {
		border-top-right-radius: 50%;
		border-bottom-right-radius: 50%;
	}
	&:nth-child(7n + 1) {
		border-top-left-radius: 50%;
		border-bottom-left-radius: 50%;
	}
}

.header-date .header-date-item .datepicker-range  {
    min-width: 0;
    width: 100%;
}

.header-date .header-date-item .datepicker input  {
    cursor: pointer;
    padding: 0 0 0 12px;
    position: relative;
    top:4px;
    height: 36px;
}

.header-date .header-date-item .select .vs__dropdown-menu {
   min-width: 80px;
   top:34px;
   left: -1px;
   z-index: 1000000;
}

.header-date .header-date-item .datepicker-range:before {
	display: none;
}

.header-date-icom-svg {
    position: absolute;
    width:18px;
    height: 18px;
    z-index: 100000;
    left: 194px;
    top: 8px;
}

.header-date-label {
    position: absolute;
    z-index: 100000;
    top: -14px;
    background-color: #fff;
    left: 15px;
    padding-left: 10px;
    padding-right: 10px;
    text-transform: uppercase;
}

/* -----------------------sortable------------------------------ */
.header-date-sortable-icon-svg-1 {
    position: relative;
    left: 3px;
}

.header-date-sortable-svg-close-arrow {
   position: relative;
    left: -12px;
    top: 0px;
    
}
.header-date-sortable-svg-open-arrow {
     position: relative;
    left: -12px;
    top: 0px;
}

.header-date-sortable-icon-svg-2 {
    position: absolute;
    top: 3px;
}

.header-date-sortable{
    position: absolute;
    background-color:#4bbac5;
    width:42px;
    height: 42px;
    top: 0;
    right: 0px;
    cursor: pointer;
    z-index: 100000;
}

.header-date-sortable-icon-box {
    position: relative;
    transform: scale(-1, 1);
    left: 1px;
    top: 10px;
}

.header-date-sortable-svg-open-box {
    position: absolute;
    transform: scale(-1, 1);
    left: 6px;
    top: 10px;
}

.header-date-sortable-svg-slose-box {
    position: absolute;
    transform: scale(-1, 1);
    left: 7px;
    top: 10px; 
}

/* ----------------------------------------------------------- */
.header-check-sortable-icon-svg-1 {
    position: relative;
    left: 3px;
}

.header-check-sortable-svg-close-arrow {
   position: relative;
    left: -12px;
    top: 0px; 
}

.header-check-sortable-svg-open-arrow {
     position: relative;
    left: -12px;
    top: 0px;
}

.header-check-sortable-icon-svg-2 {
    position: absolute;
    top: 3px;
}

.header-check-sortable{
    background-color:#4bbac5;
    width: 42px;
    height: 42px;
    cursor: pointer;
    display: flex;
    position: relative;
    top: -4px;
}

.header-check-sortable-icon-box {
    position: relative;
    transform: scale(-1, 1);
    left: 29px;
    top: 13px;
    width: 16px;
    height: 16px;
}

.header-check-sortable-svg-slose-box {
    position: absolute;
    transform: scale(-1, 1);
    left: 13px;
    top: 13px;
}

.header-check-sortable-svg-open-box {
    position: absolute;
    transform: scale(-1, 1);
    left: 13px;
    top: 13px;
}

.datepicker:before {
	content: unset;
}

// .form_element-datepicker{
//     width: 19px;
//     height: 19px;
//     background-image: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="18" height="18" viewBox="-18739 -13215 18 18"><g id="ic_date" transform="translate(-2086 -6526)"><g id="ic_date-2" data-name="ic_date" transform="translate(-17697 -6808)" opacity="0.38"><path id="Path_162" data-name="Path 162" d="M7,8H5v2H7Zm3,0H8v2h2Zm3,0H11v2h2Zm1.4-5H14V1H12V3H6V1H4V3H3.6C2.7,3,2,4.1,2,5V15a2.334,2.334,0,0,0,2,2H14a2.334,2.334,0,0,0,2-2V5C16,4.1,15.3,3,14.4,3ZM14,15H4V7H14Z" transform="translate(1044 119)"/><path id="Path_163" data-name="Path 163" d="M0,0H18V18H0Z" transform="translate(1044 119)" fill="none"/></g></g></svg>');
//     background-position: 94%;
//     background-repeat: no-repeat;
// } 

.picker-title {
	display: block;
	font-size: 12px;
	font-weight: 600;
	background-color: #fff;
	color: #a2a2b9;
	padding: 2px 10px;
	position: absolute;
	top: -13px;
	left: 50%;
	z-index: 100000;
	transform: rotateX(0) translateX(-50%);
	overflow: hidden;
	text-overflow: ellipsis;
	white-space: nowrap;
	border-radius: 4px;
	box-shadow: 0 2px 4px #cfcde5;
	
}
.placeholder-enter-active {
	animation: open-placeholder .2s;

	@keyframes open-placeholder {
		0% { transform: rotateX(-90deg) translateX(-50%);}
		100% { transform: rotateX(0) translateX(-50%); }
	}
}
.placeholder-leave-active {
	animation: close-placeholder .2s;

	@keyframes close-placeholder {
		100% { transform: rotateX(-90deg) translateX(-50%); }
	}
}
}
</style>