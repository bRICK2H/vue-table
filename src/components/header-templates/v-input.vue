<template>
	<div class="vi-container"
		:class="classes"
		:style="setStyleContainer"
	>
		<label v-if="label"
			class="vi-label"
			:for="uniq"
		>
			{{ label }}
		</label>
		<input class="vi-input"
			type="text"
			:id="uniq"
			:ref="uniq"
			:class="elClass"
			:placeholder="isPlaceholder ? '' : placeholder"
			:value="getValue"
			@input="$emit('input', $event.target.value)"
			@blur="isPlaceholder = false"
			@focus="isPlaceholder = true"
		>
		<transition name="placeholder">
			<span v-show="isPlaceholder && raisePlaceholder || value"
				class="select-title v-selected__select-title"
				:style="{ maxWidth: width === 'auto' ? `calc(100% - 10px)` : `${width - 20}px` }"
			>
				{{ placeholder }}
			</span>
		</transition>
	</div>
</template>

<script>
export default {
	name: 'VInput',
	props: {
		value: null,
		label: {
			type: String,
			default: null
		},
		placeholder: {
			type: String,
			default: ''
		},
		raisePlaceholder: {
			type: Boolean,
			default: false
		},
		width: {
			type: [Number, String],
			default: 'auto'
		},
		margin: {
			type: [Number, String],
			default: ''
		},
		outsideFocus: {
			type: Boolean,
			default: false
		},
		classes: {
			type: Array,
			default: () => ([])
		},
		elClass: {
			type: [String, Array],
			default: ''
		}
	},
	data: () => ({
		uniq: `v-input:${String(Math.random()).slice(2, 12)}`,
		isPlaceholder: false
	}),
	computed: {
		getValue() {
			return this.value
		},
		setStyleContainer() {
			return {
				width: this.width === 'auto' ? '100%' : `${this.width}px`,
				margin: String(this.margin).split(' ').map(p => `${p}px`).join(' ')
			}
		}
	},
	watch: {
		outsideFocus: {
			immediate: true,
			async handler(isFocus) {
				await this.$nextTick()

				if (isFocus) {
					this.$refs[this.uniq].focus()
					this.isPlaceholder = true
				}
			}
		},
	}
}
</script>

<style lang="scss">
	.vi-container {
		font-size: 14px;
		position: relative;
	}
	.vi-label {
		display: block;
		margin-bottom: 10px;
		color: #1f1f33;
		font-weight: 600;
	}
	.vi-input {
		width: 100%;
		min-height: 48px;
		border: 2px solid #EEEDF7;
		border-radius: 12px;
		outline: none;
		padding: 0 12px;
		text-overflow: ellipsis;
		font-size: 14px;
		font-weight: 500;
		color: #1F1F33;

		&:focus {
			border: 2px solid #cfcde5;
		}
		&::placeholder {
			color: #b7b7cc;
			font-weight: 100;
		}
	}
	.select-title {
		display: block;
		font-size: 12px;
		font-weight: 600;
		background-color: #fff;
		color: #a2a2b9;
		padding: 2px 10px;
		position: absolute;
		top: -13px;
		left: 50%;
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
</style>