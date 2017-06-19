<template>
  <transition name="slideleft">
    <div class="food" v-show="show" ref="food">
      <div class="food-content">
        <div class="head-img">
          <img class='img' :src="food.image" alt="">
          <div @click='hideDetail' class="back">
            <i class="icon-arrow_lift"></i>
          </div>
        </div>
        <div class="food-info">
          <h1 class="title" v-text="food.name"></h1>
          <div class="sell">
            <span class="sell-count">月售{{food.sellCount}}份</span>
            <span class="rating">好评率{{food.rating}}%</span>
          </div>
          <div class="price-buy">
            <div class="price">
              <span class="cur-price" v-text="'￥'+food.price"></span>
              <span class="old-price" v-if="food.oldPrice" v-text="'￥'+food.oldPrice"></span>
            </div>
            <div class="cart-wrapper">
              <cart-control ref="cart" :food='food' @cart-add='drop'></cart-control>
            </div>
            <transition name="fade">
              <span class="join-shopcart" @click.stop='addFirst' v-show="!food.count">加入购物车</span>
            </transition>
          </div>
        </div>
        <div v-if="food.info" class="prod-introduce">
          <div class="prod-text">商品介绍</div>
          <div class="prod-desc">
            <p class="prod-info" v-text="food.info"></p>
          </div>
        </div>
        <div class="rate-wrapper">
          <rate-control :tabActiveType='rateType' :active='hasContent' :rates='food.ratings' @toggleContent='toggleContent' @toggleType='toggleType'></rate-control>
        </div>
        <div class="rate-list">
          <ul>
            <li v-show="needShow(rate.text,rate.rateType)" class="rate-item border-b-1px" v-for="rate in food.ratings">
              <div class="rate-info">
                <span class="time">{{rate.rateTime | formatDate}}</span>
                <div class="rate-user">
                  <span class="rate-u" v-text="rate.username"></span>
                  <img class="user-img" :src="rate.avatar" alt="">
                </div>
              </div>
              <div class="rate-content">
                <i :class='rate.rateType==0 ? "icon-thumb_up" : "icon-thumb_down"'></i>
                <span class="rate-text" v-show="rate.text" v-text="rate.text"></span>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
import BScroll from 'better-scroll'
import RateControl from '@/components/ratecontrol/ratecontrol'
import CartControl from '@/components/cartcontrol/cartcontrol'

export default {
  props: {
    food: {
      type: Object
    }
  },
  data() {
    return {
      show: false,
      hasContent: true,
      rateType: -1
    }
  },
  methods: {
    drop(event) {
      this.$emit('addCart', event)
    },
    addFirst(event) {
      if (!event._constructed) { return false }
      // 触发小球动画
      this.$emit('addCart', event.target)
      this.$set(this.food, 'count', 1)
    },
    showDetail() {
      this.show = true
      this.hasContent = true
      this.rateType = -1
      this.$nextTick(() => {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs.food, {
            click: true
          })
        } else {
          this.scroll.refresh()
        }
      })
    },
    hideDetail() {
      this.show = false
    },
    needShow(text, type) {
      // 如果查看有内容的评价, 但是内容为空, 则不现实
      if (this.hasContent && !text) {
        return false
      }
      // 如果查看所有的评价
      if (this.rateType === -1) {
        return true
      } else {
        return this.rateType === type
      }
    },
    // 过滤评价
    toggleType(type) {
      this.rateType = type
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    },
    toggleContent(hasContent) {
      this.hasContent = hasContent
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    }
  },
  filters: {
    formatDate(date) {
      let d = new Date(date)
      let year = d.getFullYear()
      let month = (d.getMonth() + 1).toString()
      month = ('00' + month).substr(month.length)
      let day = d.getDate()
      let hour = d.getHours()
      let minute = d.getMinutes()
      return year + '-' + month + '-' + day + ' ' + hour + ':' + minute
    }
  },
  components: {
    RateControl,
    CartControl
  }
}
</script>

<style lang='scss'>
@import '../../common/sass/mixin.scss';

.food {
  position: fixed;
  top: 0;
  left: 0;
  bottom: 48px;
  background-color: #f3f5f7;
  width: 100%;
  z-index: 30;
  transform: translate3d(0, 0, 0);
  &.slideleft-enter-active,
  &.slideleft-leave-active {
    transition: all 0.3s linear;
  }
  &.slideleft-enter,
  &.slideleft-leave-active {
    transform: translate3d(100%, 0, 0);
  }
  .head-img {
    width: 100%;
    padding-top: 100%;
    position: relative;
    .img {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
    }
    .back {
      position: absolute;
      top: 10px;
      left: 0;
      width: 20px;
      height: 20px;
      padding: 10px;
      font-size: 20px;
      z-index: 31;
      color: #fff;
    }
  }
  .food-info {
    padding: 18px;
    background-color: #fff;
    margin-bottom: 16px;
    border-bottom: 1px solid rgba(7, 17, 27, 0.1);
    .title {
      font-size: 14px;
      font-weight: 700;
      color: rgb(7, 17, 27);
      line-height: 14px;
      margin-bottom: 8px;
    }
    .sell {
      margin-bottom: 18px;
      font-size: 0;
      color: rgb(147, 153, 159);
      .sell-count {
        font-size: 10px;
        margin-right: 12px;
      }
      .rating {
        font-size: 10px;
      }
    }
    .price-buy {
      position: relative;
      display: flex;
      justify-content: space-between;
      .price {
        font-weight: 700;
        .cur-price {
          font-size: 14px;
          color: rgb(240, 20, 20);
          line-height: 24px;
        }
        .old-price {
          font-size: 10px;
          text-decoration: line-through;
          color: rgb(147, 153, 159);
          line-height: 24px;
        }
      }
      .join-shopcart {
        position: absolute;
        right: 0;
        top: 0;
        display: inline-block;
        width: 74px;
        height: 24px;
        padding: 6px 12px;
        font-size: 10px;
        color: #fff;
        line-height: 12px;
        border-radius: 24px;
        text-align: center;
        box-sizing: border-box;
        background-color: rgb(0, 160, 220);
        &.fade-enter-active,
        &.fade-leave-active {
          transition: opacity 0.3s;
        }
        &.fade-enter,
        &.fade-leave-active {
          opacity: 0;
        }
      }
      .cart-wrapper {
        position: absolute;
        right: 0;
        top: -5px;
      }
    }
  }
  .prod-introduce {
    background-color: #fff;
    border-top: 1px solid rgba(7, 17, 27, 0.1);
    border-bottom: 1px solid rgba(7, 17, 27, 0.1);
    padding: 18px;
    margin-bottom: 16px;
    .prod-text {
      font-size: 14px;
      margin-bottom: 6px;
    }
    .prod-desc {
      padding: 0 8px;
      .prod-info {
        font-size: 12px;
        color: rgb(77, 85, 93);
        line-height: 24px;
      }
    }
  }
  .rate-list {
    padding: 0 18px;
    background-color: #fff;
    .rate-item {
      padding: 16px 0;
      @include border-b-1px(rgba(7, 17, 27, 0.1));
      .rate-info {
        display: flex;
        font-size: 10px;
        line-height: 12px;
        color: rgb(147, 153, 159);
        justify-content: space-between;
        margin-bottom: 6px;
        .rate-user {
          font-size: 0;
          .rate-u {
            font-size: 10px;
          }
          .user-img {
            width: 12px;
            height: 12px;
            margin-left: 6px;
            vertical-align: top;
            border-radius: 50%;
          }
        }
      }
      .rate-content {
        font-size: 0;
        [class^="icon-thumb"],
        [class*="icon-thumb"] {
          font-size: 12px;
          line-height: 24px;
          margin-right: 4px;
        }
        .icon-thumb_up {
          color: rgb(0, 160, 220);
        }
        .icon-thumb_down {
          color: rgb(147, 153, 159);
        }
        .rate-text {
          color: rgb(7, 17, 27);
          font-size: 12px;
          line-height: 16px;
        }
      }
    }
  }
}
</style>
