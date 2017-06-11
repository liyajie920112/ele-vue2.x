<template>
  <div class="shopcart">
    <div class="content">
      <div class="content-left">
        <div class="logo-wrapper">
          <div class="logo">
            <i class="icon-shopping_cart"></i>
          </div>
        </div>
        <div class="price">￥0</div>
        <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
      </div>
      <div class="content-right">
        <div class="pay">￥{{minPrice}}起送</div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  props: {
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
          .icon-shopping_cart {
            vertical-align: top;
            font-size: 24px;
            line-height: 44px;
          }
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
