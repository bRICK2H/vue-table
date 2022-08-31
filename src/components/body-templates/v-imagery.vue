<template>
	<div>
		<VImagery
			:height="component.props.height ? component.props.height : 36"
			:width="component.props.width ? component.props.width : 36"
			:itemType="component.props.itemType ? component.props.itemType : 'service'"
			:itemId="component.props.itemId ? component.props.itemId : 0"
			:value="value"
			@input="inputValue"
		/>
	</div>
</template>

<script>
import { bus } from '../../main'
import VImagery from '@inowave/izi-imagery'

export default {
	name: 'VSelfImagery',
	components: {
		VImagery
	},
	props: {
		value: null,
		component: {
			type: Object,
			default: () => ({})
		},
	},
	methods: {
		inputValue(val) {
			bus.$emit('handler', {
				type: 'image-manager',
				id: this.component.props.itemId,
				pub_photo: val[0]
			})
		}
	}
}
</script>