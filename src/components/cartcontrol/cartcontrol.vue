<template>
  <div class="cartcontrol">
    <transition name='slide'>
      <div @click='decreaseCart' class="cart-decrease icon-remove_circle_outline" v-show="food.count > 0"></div>
    </transition>
    <transition name='fade'>
      <div class="cart-count" v-show="food.count > 0" v-text="food.count">2</div>
    </transition>
    <div class="cart-increase icon-add_circle" @click='addCart'></div>
  </div>
</template>
<script>
export default {
  props: {
    food: {
      type: Object
    }
  },
  created() {
  },
  methods: {
    addCart(event) {
      // 防止在pc端点击的时候出现两次点击事件的情况, 我们只采用我们自家派发的事件
      if (!event._constructed) { return }
      if (!this.food.count) {
        // 因为如果我们修改之前没有的属性那么vue是检测不到的, 我们需要使用$set方法取给这个对象添加上这个属性才可以检测到这个属性的变化
        this.$set(this.food, 'count')
        this.food.count = 1
      } else {
        this.food.count++
      }
    },
    decreaseCart(event) {
      if (!event._constructed) { return }
      this.food.count--
    }
  }
}
</script>
<style lang="scss">
.cartcontrol {
  font-size: 0;
  .cart-decrease,
  .cart-increase {
    display: inline-block;
    font-size: 24px;
    line-height: 24px;
    color: rgb(0, 160, 220);
    padding: 6px;
  }
  .cart-decrease {
    &.slide-enter-active,
    &.slide-leave-active {
      transition: all 0.5s;
    }
    &.slide-enter,
    &.slide-leave-active {
      opacity: 0;
      transform: translate3d(36px, 0, 0) rotateZ(180deg);
    }
  }
  .cart-count {
    vertical-align: top;
    display: inline-block;
    text-align: center;
    width: 12px;
    color: rgb(147, 153, 159);
    margin-top: 6px;
    font-size: 10px;
    line-height: 24px;
    &.fade-enter-active,
    &.fade-leave-active {
      transition: all 0.5s;
    }
    &.fade-enter,
    &.fade-leave-active {
      opacity: 0;
    }
  }
}
</style>
