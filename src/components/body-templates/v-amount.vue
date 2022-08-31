<template>
    <div class="amount" ref="amountData" style="text-align: right; width: 100%;">
     {{ transactionAmount(component.value) }}
   </div>
</template>

<script>
export default {
    name: 'vSelfamount',
    props: {
    component: {
      type: Object,
      default: () => ({})
    }
  },
    methods: {
        transactionAmount(amount) {
      this.$nextTick(() => {
        const amountData = this.$refs['amountData']
        if(amount < 0) {
          amountData.style.color = "#EC4848"
          amountData.textContent = this.formatAmount(amount)
          return amountData
        } else {
          amountData.style.color = "#4FD1C5"
          amountData.textContent = `+${this.formatAmount(amount)}`
        }
      })
    },
        formatAmount: function (val) { return this.component.props.currency.prefix + (new Intl.NumberFormat('ru-Ru').format(parseFloat(val).toFixed(2))) + this.component.props.currency.postfix; },
    }

}

</script>

<style>

</style>