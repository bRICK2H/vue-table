<template>
	<div class="v-status">
		<span class="v-status-color"
			:style="{ background: component.props.color }"
		></span>
		<span class="v-status-title">
			{{ component.value }}
		</span>
		<svg width="16" height="16" viewBox="0 0 16 16" fill="none" xmlns="http://www.w3.org/2000/svg">
			<g clip-path="url(#clip0)">
				<path d="M7.99968 14.6666C11.6816 14.6666 14.6663 11.6818 14.6663 7.99992C14.6663 4.31802 11.6816 1.33325 7.99968 1.33325C4.31778 1.33325 1.33301 4.31802 1.33301 7.99992C1.33301 11.6818 4.31778 14.6666 7.99968 14.6666Z" stroke="#B7B7CC" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
				<path d="M8 10.6667V8" stroke="#B7B7CC" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
				<path d="M8 5.33325H8.00708" stroke="#B7B7CC" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
			</g>
		<defs>
			<clipPath id="clip0">
				<rect width="16" height="16" fill="white"/>
			</clipPath>
		</defs>
		</svg>
	</div>
</template>

<script>
export default {
	name: 'vSelfStatus',
	props: {
		component: {
			type: Object,
			default: () => ({})
		}
	}
}
</script>

<style lang="scss">
	.v-status {
		display: flex;
		align-items: center;		
	}
	.v-status-color {
		display: block;
		border-radius: 50%;
		width: 8px;
		height: 8px;
		margin-right: 10px;
	}
	.v-status-title {
		margin-right: 10px;
	}
</style>