<template>
  <div class="shopcart">
    <div class="content">
      <div class="content-left">
        <div class="logo-wrapper">
          <div class="logo" :class='{active:active}'>
            <i class="icon-shopping_cart"></i>
          </div>
          <div class="num" v-show="active">{{totalCount}}</div>
        </div>
        <div class="price" :class='{active:active}'>￥{{totalPrice}}</div>
        <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
      </div>
      <div class="content-right" :class='{payActive : payActive}'>
        <div class="pay">{{payDesc}}</div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  props: {
    selectFoods: {
      type: Array,
      default() {
        return [{ price: 5, count: 4 }]
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
  computed: {
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
  data() {
    return {}
  }
}
</script>
<style lang="scss">
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
  }
}
</style>
