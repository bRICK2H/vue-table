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

			<VNumeric v-if="isEdit"
				:decimal="2"
				:value="localValue"
				:focusOnOpen="true"
				width="auto"
				currency="ru"
				@input="input"
				@keydown.native.enter="saveNewValue"
				@keydown.native.esc="setDefaultValue"
				@blur="blur"
			/>
		</div>
		
		<!-- <transition name="toggle-button">
			<div v-show="component.props.showEdit && !isEdit || isHover && !isEdit"
				class="input-box-icon"
			>
				<span class="input-edit-icon"
					@click="isEdit = !isEdit"
				>
					<IEdit />
				</span>

				<span v-show="component.props.isPrice"
					class="input-modify-icon"
					@click="subHandler"
				>
					<ISettings />
				</span>
			</div>
		</transition> -->

		<!-- <transition name="toggle-button">
			<div v-show="isShowCancel"
				class="input-box-icon"
			>
				<span class="input-clear-icon"
					title="Отменить"
					@click="setDefaultValue"
				>
					<ICloseRed />
				</span>

				<span class="input-save-icon"
					title="Сохранить"
					@click="saveNewValue"
				>
					<ISave />
				</span>
			</div>
		</transition> -->

		<!-- <div class="input-box-icon"
			v-if="component.props.isPrice && !isShowCancel"
		>
			<span class="input-edit-icon"
				@click="isEdit = !isEdit"
			>
				<IEdit />
			</span>

			<span class="input-modify-icon"
				@click="subHandler"
			>
				<ISettings />
			</span>
		</div>
		
		<div v-else-if="component.props.isPrice"
			class="input-box-icon"
		>
			<span class="input-clear-icon"
				title="Отменить"
				@click="setDefaultValue"
			>
				<ICloseRed />
			</span>

			<span class="input-save-icon"
				title="Сохранить"
				@click="saveNewValue"
			>
				<ISave />
			</span>
		</div> -->

		<transition name="toggle-button">
			<div class="input-box-icon"
				v-if="component.props.isPrice && !isShowCancel"
			>
				<span class="input-edit-icon"
					@click="isEdit = !isEdit"
				>
					<IEdit />
				</span>

				<span class="input-modify-icon"
					@click="subHandler"
				>
					<ISettings />
				</span>
			</div>
		</transition>
		
		<transition name="toggle-button">
			<div v-if="component.props.isPrice && isShowCancel"
				class="input-box-icon"
			>
				<!-- Clear -->
				<span class="input-clear-icon"
					title="Отменить"
					@click="setDefaultValue"
				>
					<ICloseRed />
				</span>

				<!-- Save -->
				<span class="input-save-icon"
					title="Сохранить"
					@click="saveNewValue"
				>
					<ISave />
				</span>
			</div>
		</transition>

	</div>
</template>

<script>
import { bus } from '../../main'
import VNumeric from '@inowave/izi-numeric'
import IEdit from './icon/edit'
import ISave from './icon/save'
import ICloseRed from './icon/close-red'
import ISettings from './icon/settings'

export default {
	name: 'vSelfInput',
	components: {
		IEdit,
		ISave,
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
	}),
	methods: {
		input(val) {
			this.isShowCancel = this.initValue !== val
			this.localValue = val
		},
		setDefaultValue() {
			this.isEdit = false
			this.localValue = this.initValue
			this.$emit('input', this.initValue)
		},
		async saveNewValue() {
			const { service_id, type } = this.component.props.objectPrice
			const pData = {
				data: {
					service: service_id,
					values: [[type, this.localValue]]
				}
			}
			const response = await this.$ajaxData('/endata/action/price-set', pData, 'encoded')

			try {
				if (response.result) {
					this.$emit('input', this.localValue)
				}
			} catch (error) {
				console.error(error)
			}
			
			this.isEdit = false
			this.isShowCancel = false
			this.initValue = this.localValue
		},
		blur() {
			this.isEdit = false
			this.isShowCancel = false
		},
		subHandler() {
			bus.$emit('handler', {
				...this.component.props.objectPrice,
				price: this.localValue,
				type: 'price'
			})
		}
	},
	created() {
		this.initValue = this.localValue = this.value
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
	.input-modify-icon {
		cursor: pointer;
		opacity: .7;

		&:hover {
			opacity: 1;
		}
	}

	.toggle-button-enter-active {
		animation: toggle-button-enter .8s;

		@keyframes toggle-button-enter {
			0% { opacity: 0 }
		}
	}
	.toggle-button-leave-active {
		animation: toggle-button-leave .4s;

		@keyframes toggle-button-leave {
			100% { opacity: 0 }
		}
	}
</style>