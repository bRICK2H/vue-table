<template>
	<span class="v-self-handler">
		<span class="v-self-handler-title"
			@click="handler('service-modal')"
		>
			{{ component.value }}
		</span>
		<div v-if="isEdit"
			class="v-self-handler-input-box"
		>
			<input class="v-self-handler-input"
				type="text"
				ref="v-self-input"
				:value="localValue"
				@input="inputValue($event.target.value)"
				@keydown.enter="keyPress('enter')"
				@keydown.esc="keyPress('esc')"
				@blur="blur"
			>
			
			<span v-show="isDiffValue"
				class="v-self-handler-icon-save"
				@click.once="buttonHandler('service')"
			>
				<ISave />
			</span>
		
			<span v-show="isEdit && !isDiffValue"
				ref="v-self-close"
				class="v-self-handler-icon-close"
				@click="closeInput"
			>
				<IClose />
			</span>
		</div>
		
		<span v-else
			class="v-self-handler-icon-edit"
			@click="hideEdit"
		>
			<IEdit />
		</span>
	</span>
</template>

<script>
import { bus } from '../../../main'
import IEdit from '../icon/edit'
import ISave from '../icon/save'
import IClose from '../icon/close-red'

export default {
	name: 'VPriceEditTitle',
	components: {
		IEdit,
		ISave,
		IClose
	},
	props: {
		value: null,
		component: {
			type: Object,
			default: () => ({})
		},
	},
	data: () => ({
		initValue: '',
		localValue: '',
		isEdit: false,
		buttonHandler: () => ({})
	}),
	computed: {
		isDiffValue() {
			return this.initValue !== this.localValue
		}
	},
	methods: {
		inputValue(val) {
			this.localValue = val
		},
		handler(type) {
			const { id, pub_title } = this.component.props

			bus.$emit('handler', {
				id,
				type,
				pub_title,
				title: this.localValue,
			})
		},
		hideEdit() {
			this.isEdit = true
		},
		closeInput() {
			this.isEdit = false
		},
		keyPress(type) {
			this.$refs['v-self-input'].blur()
			this.$refs['v-self-close'].click()

			if (type === 'esc') {
				this.localValue = this.value
			} else if (this.isDiffValue) {
				this.handler('service')
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
	},
	watch: {
		async isEdit(val) {
			if (val) {
				await this.$nextTick()
				this.$refs['v-self-input'].focus()
			}
		},
		'component.props.isEdit': {
			immediate: true,
			handler(val) {
				if (val) this.isEdit = val
			}
		},
		value(val) {
			this.closeInput()
			this.initValue = this.localValue = val
		}
	},
	async created() {
		this.initValue = this.localValue = this.component.value
		document.body.addEventListener('click', () => this.buttonHandler(false))
	}
}
</script>

<style lang="scss">
	.v-self-handler {
		display: inline-flex;
		align-items: center;
		position: relative;
	}
	.v-self-handler-title {
		transition: .2s;
		cursor: pointer;
		border-bottom: 1px solid transparent;
		padding-bottom: 2px;

		&:hover {
			border-bottom: 1px solid #a8a8c5;
		}
	}
	.v-self-handler-input-box {
		width: 100%;
		display: inline-flex;
    	width: calc(100% + 30px);
		text-align: center;
		position: absolute;
	}
	.v-self-handler-input {
		width: 0;
		height: 33px;
		flex: 1 1 100%;
		padding: 0 10px;
		border: 2px solid #eeedf7;
    	border-radius: 8px
	}
	.v-self-handler-icon-edit {
		cursor: pointer;
		margin-left: 15px;
		opacity: .3;

		&:hover {
			opacity: 1;
		}
	}
	.v-self-handler-icon-save,
	.v-self-handler-icon-close {
		cursor: pointer;
		position: absolute;
		right: -30px;
		top: 50%;
    	transform: translateY(-50%);
		 opacity: .3;

		&:hover {
			opacity: 1;
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