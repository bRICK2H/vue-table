<template>
  <div class="type-transaction-wrapper">
      <p class="type-transaction__title">{{component.value}}</p>
      <p class="payment_method__title">{{component.props.type}}</p>
  </div>
</template>

<script>
export default {
    name: 'vSelftypeTransaction',
    props: {
		component: {
			type: Object,
			default: () => ({})
		}
	},
}
</script>

<style>
.type-transaction-wrapper .type-transaction__title {
    font-style: normal;
    font-weight: normal;
    font-size: 14px;
    line-height: 20px;
    color: #1F1F33;
}
.type-transaction-wrapper .payment_method__title {
    font-style: normal;
    font-weight: 500;
    font-size: 12px;
    line-height: 16px;
    color: #A2A2B9;
}
</style>