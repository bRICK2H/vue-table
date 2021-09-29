<template>
	<div class="v-body-cell"
		:style="{ minWidth: `${hcellWidth}px`, justifyContent: side }"
	>		
	
		<!-- PAYMENT -->
		<template v-if="type === 'payment'">
			<span class="v-body-cell-more-box">
				<span v-for="(value, i) of defineValue"
					:key="i"
					class="v-body-cell-more-item"
				>
					{{ value }}
				</span>
			</span>
		</template>

		<!-- FILE -->
		<template v-else-if="type === 'file'">
			<a class="v-body-cell-file"
				:href="defineValue.path" download>
				<span class="v-body-cell-pdf"></span>
				{{ defineValue.title }}
			</a>
		</template>

		<!-- SUM -->
		<template v-else-if="type === 'sum'">
			<span class="v-body-cell-sum"
				:class="{ 'v-body-cell-sum--negative': !defineValue.isPositive }"
			>
				{{ defineValue.amount }} {{ defineValue.currency }}
			</span>
		</template>
		
		<!-- SELF COMPONENT -->
		<template v-else-if="type === 'self'">
			{{value.value}}
			<component
				:is="value.component.name"
				v-bind="{ component: value.component }"
			/>
		</template>
		

		<!-- ELSE -->
		<template v-else>
			<span>
				{{ this.value }}
			</span>
		</template>

	</div>
</template>

<script>
export default {
	name: 'VBodyCell',
	props: {
		value: null,
		type: {
			type: String,
			default: 'default' 
		},
		side: {
			type: String,
			default: 'flex-start'
		},
		hcellWidth: {
			type: Number,
			default: 0
		},
	},
	computed: {
		// Обработка значений для конкретного шаблона
		defineValue() {
			switch (this.type) {
				case 'payment': {
					const typeOfMany = Array.isArray(this.value)
						? 'array'
						: typeof this.value

					return typeOfMany === 'object'
						? Object.values(this.value)
						: this.value
				}

				case 'file': {
					const { title, path } = this.value

					return {
						title: title ? title : 'Скачать',
						path: path ? path : '/' 
					}
				}

				case 'sum': {
					const result = []
					const { amount, currency } = this.value
					const isPositive = +String(amount).replace(/ /, '') > 0
					const arrAmount = String(amount).replace(/[\D]/, '').split('')

					while(arrAmount.length > 3) {
						result.unshift(...[' ', ...arrAmount.splice(-3)])
					}

					result.unshift(...[isPositive ? '+ ' : '- ', ...arrAmount])

					return {
						currency,
						isPositive,
						amount: result.join(''),
					}
				}

				default: {
				  return this.value
				}
			}
		}
	}
}
</script>

<style lang="scss">
	.v-body-cell {
		display: flex;
		font-size: 14px;
		font-weight: 400;
		
		&:not(:last-of-type) {
			margin-right: 35px;
		}
		&:last-of-type {
			margin: auto 0 auto auto;
		}
	}
	.v-body-cell-more-box {
		display: flex;
		flex-direction: column;
	}
	.v-body-cell-more-item {
		
		&:not(:first-of-type) {
			font-size: 12px;
			color: #a2a2b9;
			font-weight: 500;
		}
		&:not(:last-of-type) {
			margin-bottom: 5px;
		}
	}

	// file
	.v-body-cell-file {
		font-size: 12px;
		font-weight: 500;
		color: #a2a2b9;
		display: flex;
		flex-direction: column;
		align-items: center;
		text-decoration: none;
		transition: .2s;

		&:hover {
			filter: brightness(.6);
		}
	}
	.v-body-cell-pdf {
		width: 20px;
		height: 24px;
		background: url('../../assets/img/svg/pdf.svg') no-repeat center / cover;
		margin-bottom: 7px;
	}

	// sum
	.v-body-cell-sum {
		font-size: 14px;
		font-weight: 500;
		color: #4fd1c5;

		&--negative {
			color: #ec4848;
		}
	}
</style>