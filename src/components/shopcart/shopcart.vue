<template>
  <div class="shopcart">
    <div class="content">
      <div class="content-left">
        <div class="logo-wrapper" @click='toggleList'>
          <div class="logo" :class='{active:active}'>
            <i class="icon-shopping_cart"></i>
          </div>
          <div class="num" v-show="active">{{totalCount}}</div>
        </div>
        <div class="price" :class='{active:active}'>￥{{totalPrice}}</div>
        <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
      </div>
      <div class="content-right" @click='pay' :class='{payActive : payActive}'>
        <div class="pay">{{payDesc}}</div>
      </div>
      <div class="ball-wrapper">
        <div v-for="(ball,index) in balls">
          <transition name="drop" @before-enter="beforeDrop" @enter="enterDrop" @after-enter="afterDrop">
            <div class="ball" v-show="ball.show">
              <div class="inner inner-hook"></div>
            </div>
          </transition>
        </div>
      </div>
      <transition name="slide">
        <div class="shopcart-list" v-show="listShow">
          <div class="list-header">
            <h1 class="title">购物车</h1>
            <span class="empty" @click='empty'>清空</span>
          </div>
          <div ref="listContent" class="list-content">
            <ul>
              <li class="food border-b-1px" v-for="food in selectFoods">
                <span class="name" v-text="food.name"></span>
                <div class="price">
                  <span>￥{{food.count * food.price}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cart-control @cart-add='drop' :food='food'></cart-control>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </transition>
      <transition name="fade">
        <div class="list-mask" @click='toggleList' v-show="listShow"></div>
      </transition>
    </div>
  </div>
</template>
<script>
import BScroll from 'better-scroll'
import CartControl from '@/components/cartcontrol/cartcontrol'
export default {
  props: {
    selectFoods: {
      type: Array,
      default() {
        return []
      }
    },
    deliveryPrice: {
      type: Number,
      default: 0
    },
    minPrice: {
      type: Number,
      default: 0
    }
  },
  data() {
    return {
      balls: [
        {
          show: false
        }, {
          show: false
        }, {
          show: false
        }, {
          show: false
        }, {
          show: false
        }
      ],
      dropBalls: [],
      fold: true // 默认折叠
    }
  },
  computed: {
    listShow() {
      // 如果总数是0, 那么就折叠, 就是不展示
      if (!this.totalCount) {
        this.fold = true
        return false
      }
      let show = !this.fold
      if (show) {
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.listContent, {
              click: true
            })
          } else {
            this.scroll.refresh()
          }
        })
      }
      return show
    },
    // 总金额
    totalPrice() {
      let totalP = 0
      this.selectFoods.forEach((food) => {
        totalP += (food.price * food.count)
      })
      return totalP
    },
    // 购物车中商品总数量
    totalCount() {
      let total = 0
      this.selectFoods.forEach((food) => {
        total += food.count
      })
      return total
    },
    // 购物车有商品后的样式
    active() {
      return this.totalCount > 0
    },
    // 配送描述信息
    payDesc() {
      // 1. 购物车为空的时候, 显示 多少元起送
      // 2. 有商品的时候, 显示还差多少元起送
      // 3. 达到起送费的时候, 显示去结算,并修改样式
      let desc = `￥${this.minPrice}元起送`
      if (this.active) {
        let diff = this.minPrice - this.totalPrice
        if (diff > 0) {
          desc = `还差￥${diff}起送`
        } else {
          desc = '去结算'
        }
      }
      return desc
    },
    payActive() {
      return (this.minPrice - this.totalPrice) <= 0
    }
  },
  methods: {
    pay() {
      if (this.payActive) {
        window.alert(`支付${this.totalPrice}元`)
      }
      return false
    },
    toggleList() {
      if (!this.totalCount) {
        return false
      }
      this.fold = !this.fold
      return false
    },
    // 清空
    empty() {
      this.selectFoods.forEach((food, index) => {
        food.count = 0
      })
    },
    // 发射小球
    drop(el) {
      // some和forEach的区别是当返回true的时候就不再执行
      this.balls.some((item, index, arr) => {
        if (!item.show) {
          item.show = true
          item.el = el
          this.dropBalls.push(item)
          return true
        }
      })
    },
    // 动画相关, el就是需要做动画的元素
    beforeDrop(el) {
      let count = this.balls.length
      while (count--) {
        let ball = this.balls[count]
        // 找到显示的小球
        if (ball.show) {
          let rect = ball.el.getBoundingClientRect()
          // 设置小球初始位置
          let x = rect.left - 32
          let y = -(window.innerHeight - rect.top - 22)
          el.style.display = ''
          el.style.transform = `translate3d(0,${y}px,0)`
          el.style.webkitTransform = `translate3d(0,${y}px,0)`

          let inner = el.getElementsByClassName('inner-hook')[0]
          inner.style.transform = `translate3d(${x}px,0,0)`
          inner.style.webkitTransform = `translate3d(${x}px,0,0)`
        }
      }
    },
    enterDrop(el, done) {
      /* eslint-disable no-unused-vars */
      let ofh = el.offsetHeight
      this.$nextTick(() => {
        el.style.transform = 'translate3d(0,0,0)'
        el.style.webkitTransform = 'translate3d(0,0,0)'
        let inner = el.getElementsByClassName('inner-hook')[0]
        inner.style.transform = 'translate3d(0,0,0)'
        inner.style.webkitTransform = 'translate3d(0,0,0)'
        el.addEventListener('transitionend', done)
      })
    },
    afterDrop(el) {
      // 移除dropBalls中的小球
      let ball = this.dropBalls.shift()
      // 将这个小球在balls中设置为不显示
      if (ball) {
        ball.show = false
        el.style.display = 'none'
      }
    }
  },
  components: {
    CartControl
  }
}
</script>
<style lang="scss">
@import '../../common/sass/mixin.scss';

.shopcart {
  position: fixed;
  bottom: 0;
  left: 0;
  z-index: 50;
  width: 100%;
  height: 48px;
  .content {
    display: flex;
    background-color: #141d27; // height: 100%;
    color: rgba(255, 255, 255, 0.4);
    font-size: 0;
    .content-left {
      flex: 1;
      .logo-wrapper {
        position: relative;
        top: -10px;
        margin: 0 12px;
        display: inline-block;
        width: 56px;
        height: 56px;
        background-color: #141d27;
        border-radius: 50%;
        padding: 6px;
        box-sizing: border-box;
        vertical-align: top;
        .logo {
          display: inline-block;
          width: 100%;
          height: 100%;
          border-radius: 50%;
          background-color: #2b343c;
          text-align: center;
          &.active {
            background-color: rgb(0, 160, 220);
            .icon-shopping_cart {
              color: #fff;
            }
          }
          .icon-shopping_cart {
            vertical-align: top;
            font-size: 24px;
            line-height: 44px;
          }
        }
        .num {
          position: absolute;
          top: 0;
          right: 0;
          width: 24px;
          height: 16px;
          line-height: 16px;
          border-radius: 12px;
          text-align: center;
          font-size: 9px;
          font-weight: 700;
          color: #fff;
          background-color: rgb(240, 20, 20);
          box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.4);
        }
      }
      .price {
        display: inline-block;
        margin-top: 12px;
        font-size: 16px;
        font-weight: 700;
        line-height: 24px;
        color: rgba(255, 255, 255, 0.4);
        padding-right: 12px;
        box-sizing: border-box;
        border-right: 1px solid rgba(255, 255, 255, 0.1);
        &.active {
          color: #fff;
        }
      }
      .desc {
        display: inline-block;
        margin-left: 12px;
        font-size: 10px;
        font-weight: 100;
        line-height: 24px;
      }
    }
    .content-right {
      width: 105px;
      background-color: #2b343c;
      &.payActive {
        color: #fff;
        background-color: #00b43c;
      }
      .pay {
        font-size: 12px;
        line-height: 48px;
        text-align: center;
        font-weight: 700;
      }
    }
    .ball-wrapper {
      .ball {
        position: fixed;
        left: 32px;
        bottom: 22px;
        z-index: 200;
        transition: all 0.5s cubic-bezier(.34, -0.32, .81, .25);
        .inner {
          width: 16px;
          height: 16px;
          border-radius: 50%;
          background: rgb(0, 160, 220);
          transition: all 0.5s linear;
        }
      }
    }
    .shopcart-list {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      z-index: -1;
      transform: translate3d(0, -100%, 0);
      &.slide-enter-active,
      &.slide-leave-active {
        transition: all 0.3s ease-out;
      }
      &.slide-enter,
      &.slide-leave-active {
        transform: translate3d(0, 0, 0);
      }
      .list-header {
        height: 40px;
        background-color: #f3f5f7;
        border-bottom: 1px solid rgba(7, 17, 27, 0.1);
        padding: 0 18px;
        .title {
          float: left;
          font-size: 14px;
          color: rgb(7, 17, 27);
          line-height: 40px;
        }
        .empty {
          float: right;
          font-size: 12px;
          line-height: 40px;
          color: rgb(0, 160, 220);
        }
      }
      .list-content {
        background-color: #fff;
        padding: 0 18px;
        overflow: hidden;
        max-height: 217px;
        .food {
          height: 48px;
          padding: 12px 0;
          box-sizing: border-box;
          font-size: 0;
          @include border-b-1px(rgba(7, 17, 27, 0.1));
          .name {
            font-size: 14px;
            color: rgb(7, 17, 27);
            line-height: 24px;
          }
          .price {
            position: absolute;
            right: 90px;
            bottom: 12px;
            font-size: 14px;
            font-weight: 700;
            line-height: 24px;
            color: rgb(240, 20, 20);
            margin: 0 12px 0 18px;
          }
          .cartcontrol-wrapper {
            position: absolute;
            right: 0;
            bottom: 6px;
          }
        }
      }
    }
    .list-mask {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background-color: rgba(7, 17, 27, 0.6);
      -webkit-backdrop-filter: blur(10px);
      z-index: -2;
      &.fade-enter-active,
      &.fade-leave-active {
        transition: all 0.3s ease-out;
      }
      &.fade-enter,
      &.fade-leave-active {
        opacity: 0;
      }
    }
  }
}
</style>
