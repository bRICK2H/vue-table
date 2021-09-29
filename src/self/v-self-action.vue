<template>
	<div>
		<label for="upload" class="v-action">
			{{ component.props.title }}
		</label>
		<input type="file" hidden id="upload">
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
	}
}
</script>

<style lang="scss">
	.v-action {
		font-size: 14px;
		font-weight: 600;
		color: #1F1F33;
		padding: 10px 17px;
		border-radius: 12px;
		border: 1px solid #EEEDF7;
		background: #fff;
		box-shadow: 0px 0px 4px rgba(26, 32, 44, 0.06), 0px 4px 10px -1px rgba(26, 32, 44, 0.06);
		cursor: pointer;
	}
</style>