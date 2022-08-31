<template>
	<div class="v-action__wrapp">
		<div v-if="getTypeAction === 'many'"
			class="action-many-container"
		>
			<p v-for="({ type, name, path }, i) of component.props.action"
				class="action-many-box"
				:key="`${type}:${i}`"
			>
				<a v-if="type === 'download'"
					class="action-many-title"
					download
					:href="path"
				>
					<component :is="name" :type="type" />
					{{ component.value[i] }}
				</a>
				<span v-else-if="type === 'visibility'"
					class="action-many-title"
				>
					<component :is="name" :type="type" />
					{{ component.value[i] }}
				</span>
			</p>
		</div>
		
		<template v-else-if="getTypeAction === 'file'">
			<label for="upload" class="v-action">
				{{ component.value }}
			</label>
			<input type="file" class="action-file jcf-ignore" id="upload">
		</template>

		<template v-else-if="getTypeAction === 'link'">
			<a :href="component.props.path" class="v-action">
				{{ component.value }}
			</a>
		</template>

		<template v-else-if="getTypeAction === 'receipt'">
			<a class="v-action__icon__title"
				:href="component.props.path"
			>
				<!-- :[component.props.attr]="component.props.attr" -->
				<pdf class="receipt-pdf" />
				{{ component.value }}
			</a>
		</template>

	</div>
</template>
<script>
import pdf from './icon/pdf'
import eye from './icon/eye'
import receipt from './icon/receipt'

export default {
	name: 'VAction',
	components: {
		pdf, eye, receipt
	},
	props: {
		component: {
			type: Object,
			default: () => ({})
		}
	},
	computed: {
		getTypeAction() {
			return this.component?.props?.type ?? null
		}
	}
}
</script>

<style lang="scss">
	.v-action {
		font-size: 14px;
		font-weight: 600;
		color: #1F1F33;
		padding: 10px 17px;
		border: none;
		outline: none;
		border: 1px solid #EEEDF7;
		text-decoration: none;
		border-radius: 12px;
		background: #fff;
		box-shadow: 0px 0px 4px rgba(26, 32, 44, 0.06), 0px 4px 10px -1px rgba(26, 32, 44, 0.06);
		cursor: pointer;
	}
	.receipt-pdf {
		margin-bottom: 5px;
	}
	.v-action__wrapp {
		width: 100%;
		text-align: center;
		display: flex;
		justify-content: center;
	}
	.v-action__icon__title {
		font-style: normal;
		font-weight: 500;
		font-size: 12px;
		line-height: 16px;
		color: #A2A2B9;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		cursor: pointer;
	}
	.action-many-container {
		display: flex;
		justify-content: space-between;
	}
	.action-many-box {
		&:hover {
			opacity: .7;
		}
	}
	.action-many-title {
		color: #a2a2b9;
		cursor: pointer;
		display: flex;
		flex-direction: column;
		align-items: center;
	}
	.action-file {
		display: none;
	}
</style>