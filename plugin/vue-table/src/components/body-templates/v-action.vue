<template>
	<div>
		<template v-if="getTypeAction === 'file'">
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
	</div>
</template>

<script>
export default {
	name: 'vSelfaction',
	props: {
		component: {
			type: Object,
			default: () => ({})
		}
	},
	computed: {
		getTypeAction() {
			return this.component.props?.type ?? null
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
	.action-file {
		display: none;
	}
</style>