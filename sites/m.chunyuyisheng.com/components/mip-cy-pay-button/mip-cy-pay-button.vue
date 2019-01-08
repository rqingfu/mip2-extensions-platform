<template>
  <div
    class="mip-cy-pay-button"
    @click="payHandle">
    <slot>
      <span>去付款</span>
    </slot>
  </div>
</template>

<style scoped>
.mip-cy-pay-button {
  background: #51b5c7;
}
</style>

<script>
const domain = 'https://m.chunyuyisheng.com'
export default {
  props: {
    loginUrl: {
      type: String,
      default: ''
    },
    orderName: {
      type: String,
      default: ''
    },
    orderDesc: {
      type: String,
      default: ''
    },
    orderType: {
      type: String,
      default: ''
    },
    partner: {
      type: String,
      default: ''
    },
    failUrl: {
      type: String,
      default: ''
    },
    infoDict: {
      type: String,
      default: ''
    },
    autoPay: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      isPaying: false,
      errorMsg: ''
    }
  },
  mounted () {
    const that = this
    const autoPay = this.autoPay
    if (autoPay) {
      that.payHandle()
    }
  },
  methods: {
    payHandle () {
      const that = this
      const loginUrl = that.loginUrl
      const orderName = that.orderName
      const orderDesc = that.orderDesc
      const orderType = that.orderType
      const partner = that.partner
      const failUrl = that.failUrl
      const infoDict = that.infoDict
      const autoPay = this.autoPay

      if (loginUrl) {
        MIP.viewer.open(loginUrl)
      } else {
        if (that.isPaying) return
        that.isPaying = true

        fetch(`${domain}/weixin/pay/create_order/`, {
          method: 'POST',
          headers: {'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8'},
          body: `finish_fail_url=${failUrl}&order_name=${orderName}&order_type=${orderType}&partner=${partner}&order_desc=${orderDesc}&info_dict=${infoDict}`
        })
          .then(res => res.json())
          .then((res) => {
            if (res && res.baidu_pay_url) {
              if (autoPay) {
                MIP.viewer.open(res.baidu_pay_url) // 替换location.replace;
              } else {
                MIP.viewer.open(res.baidu_pay_url)
              }
            } else {
              that.$emit('show', res.error_msg)
            }
            that.isPaying = false
          }, () => {
            that.isPaying = false
          })
      }
    }
  }
}
</script>
