<template>
	<div class="input-edit-container"
		@mouseenter="isHover = true"
		@mouseleave="isHover = false"
	>

		<div class="input-edit-box">
			<span v-show="!isEdit && (component.props.isPrice || localValue > 0)"
				class="input-name-item"
			 	@dblclick="isEdit = !isEdit"
			>
				{{ component.value.toLocaleString() }} &#8381;
			</span>

			<VNumeric v-show="isEdit"
				ref="v-self-input"
				:decimal="2"
				:value="localValue"
				:toggleFocus="isEdit"
				width="auto"
				currency="ru"
				@input="inputValue"
				@keydown.native.enter="keyPress('enter')"
				@keydown.native.esc="keyPress('esc')"
				@blur="blur"
			/>
		</div>
		
		<div class="input-box-icon">
			<span v-show="!component.props.isPrice && isHover && !isEdit && !value"
				class="input-plus-icon input-plus-icon--absolute"
				title="Добавить"
				@click.once="isEdit = true"
			>
				<IPlus />
			</span>

			<span v-show="isEdit && !isShowCancel"
				ref="input-clear-icon"
				class="input-clear-icon"
				:class="{ 'input-clear-icon--absolute': !component.props.isPrice }"
				title="Отменить"
				@click="closeInput"
			>
				<ICloseRed />
			</span>

			<span v-show="!isEdit && value"
				class="input-edit-icon"
				:class="{ 'input-edit-icon--absolute': !component.props.isPrice }"
				title="Редактировать"
				@click="hideEdit"
			>
				<IEdit />
			</span>

			<span v-show="component.props.isPrice && !isShowCancel"
				class="input-modify-icon"
				title="Создать модификатор для цены"
				@click="subHandler"
			>
				<ISettings />
			</span>

			<span v-show="isShowCancel"
				class="input-save-icon input-save-icon--absolute"
				title="Сохранить"
				@click="buttonHandler('price')"
			>
				<ISave />
			</span>
		</div>

	</div>
</template>

<script>
import { bus } from '../../../main'
import VNumeric from '@inowave/izi-numeric'
import IEdit from '../icon/edit'
import ISave from '../icon/save'
import IPlus from '../icon/plus'
import ICloseRed from '../icon/close-red'
import ISettings from '../icon/settings'

export default {
	name: 'VPriceEditNumeric',
	components: {
		IEdit,
		ISave,
		IPlus,
		ICloseRed,
		VNumeric,
		ISettings
	},
	props: {
		value: null,
		component: {
			type: Object,
			default: () => ({})
		},
	},
	data: () => ({
		isEdit: false,
		isHover: false,
		isShowCancel: false,
		initValue: 0,
		localValue: 0,
		buttonHandler: () => ({})
	}),
	methods: {
		inputValue(val) {
			this.isShowCancel = this.initValue !== val
			this.localValue = val
		},
		closeInput() {
			this.isEdit = false
			this.isShowCancel = false

			this.localValue = this.initValue
		},
		hideEdit() {
			this.isEdit = true
		},
		handler(t) {
			const { service_id, type, unit_id } = this.component.props.objectPrice

			bus.$emit('handler', {
				type: t,
				data: {
					unit: unit_id,
					service: service_id,
					values: [[type, this.localValue]],
				}
			})
		},
		keyPress(type) {
			this.isEdit = false

			setTimeout(() =>  {
				this.$refs['input-clear-icon'].click()
			})
			
			if (type === 'esc') {
				this.localValue = this.value
			} else if (this.isShowCancel) {
				this.handler('price')
			}
		},
		async blur() {
			const buttonAction = await new Promise(resolve => this.buttonHandler = resolve)
			
			if (!buttonAction) {
				this.closeInput()
			} else {
				this.handler(buttonAction)
			}
		},
		subHandler() {
			bus.$emit('handler', {
				...this.component.props.objectPrice,
				price: this.localValue,
				type: 'price-modal'
			})
		}
	},
	watch: {
		value(val) {
			this.isEdit = false
			this.isShowCancel = false
			this.initValue = this.localValue = val
		},
	},
	created() {
		this.initValue = this.localValue = this.value
	},
	mounted() {
		document.body.addEventListener('click', () => this.buttonHandler(false))
	}
}
</script>

<style lang="scss">
	.input-edit-container {
		width: 100%;
		display: flex;
		align-items: center;
		position: relative;
	}
	.input-edit-box {
		height: 33px;
		display: flex;
		align-items: center;
		margin: 0 45px 0 auto;
	}
	.input-name-item {
		font-weight: 400;
	}
	.input-box-icon {
		height: 50px;
		width: 45px;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		align-items: center;
		position: absolute;
		right: -10px;
	}

	.input-clear-icon,
	.input-save-icon,
	.input-edit-icon,
	.input-modify-icon,
	.input-plus-icon {
		cursor: pointer;
		opacity: .3;

		&:hover {
			opacity: 1;
		}

		&--absolute {
			position: absolute;
			top: 50%;
			right: 10px;
			transform: translateY(-50%);
		}
	}

	// .toggle-button-enter-active {
	// 	animation: toggle-button-enter .2s;

	// 	@keyframes toggle-button-enter {
	// 		0% { opacity: 0 }
	// 	}
	// }
	// .toggle-button-leave-active {
	// 	animation: toggle-button-leave .2s;

	// 	@keyframes toggle-button-leave {
	// 		100% { opacity: 0 }
	// 	}
	// }
</style>