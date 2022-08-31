<template>
	<div class="v-icon">
		<template v-if="component.value">
			<component :is="component.props.icon" />
		</template>
	</div>
</template>
<script>
import izi from './icon/izi'

export default {
	name: 'VIcon',
	components: {
		izi
	},
	props: {
		component: {
			type: Object,
			default: () => ({})
		}
	},
}
</script>

<style lang="scss">
	.v-icon {
		display: flex;
		align-items: center;
	} 
</style>