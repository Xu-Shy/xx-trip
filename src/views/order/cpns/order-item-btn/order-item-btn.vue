<template>
  <div class="order-item-btn">
    <div class="left">
      <div v-if="itemData.orderStatus === 1" class="left-content">
        <img src="@/assets/img/order/icon-time.png" alt="" class="icon-time" />
        <span class="time">{{ remainTime }}</span>
        <span class="msg">后订单自动取消</span>
      </div>
    </div>

    <div class="right">
      <button v-if="itemData.orderStatus === 1" class="btn connect">
        联系房东
      </button>
      <button v-if="itemData.orderStatus === 1" class="btn pay">去支付</button>
      <button v-if="itemData.orderStatus === 5" class="btn delete">删除</button>
      <button v-if="itemData.orderStatus === 5" class="btn order">
        再次预定
      </button>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from "vue"

const props = defineProps({
  itemData: {
    type: Object,
    default: () => {},
  },
})

const remainTime = ref(0)
const timeId = ref(null)
let counter = 1

const startCountDown = () => {
  timeId.value = setInterval(() => {
    remainTime.value = parseInt((props.itemData.countdown - counter) / 60)
    counter++
    if (remainTime.value <= 0) {
      clearInterval(timeId.value)
      return
    }
  }, 1000)
}

onMounted(() => {
  startCountDown()
})
</script>

<style lang="less" scoped>
.rowlayout {
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-direction: row;
}

.order-item-btn {
  .rowlayout();
  height: 60px;

  .left-content {
    .rowlayout();
    font-size: 11px;
    .icon-time {
      width: 11px;
      height: 11px;
      margin-right: 3px;
    }
    .time {
      color: var(--primary-color);
      letter-spacing: 0;
      margin-right: 1px;
    }
    .msg {
      color: #666;
      letter-spacing: 0;
      line-height: 11px;
    }
  }

  .right {
    .rowlayout();
    .btn {
      display: inline-block;
      text-align: center;
      line-height: 30px;
      margin-left: 10px;

      font-size: 14px;
      min-width: 75px;
      color: #666;
      background: #fff;
      border: 1px solid #e9e9e9;
      border-radius: 6px;
    }

    .connect {
    }
    .pay {
      color: white;
      background-color: var(--primary-color, #ff9645);
      border: 1px solid var(--primary-color, #ff9645);
    }
    .delete {
    }
    .order {
      color: var(--primary-color, #ff9645);
      background-color: #fff;
      border: 1px solid var(--primary-color, #ff9645);
    }
  }
}
</style>
