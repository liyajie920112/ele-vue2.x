<template>
  <div class='seller-tab'>
    <div class="container">
      <div class="header">
        <div class="header-info border-b-1px">
          <h1 class="title" v-text="seller.name"></h1>
          <div class="star-wrapper">
            <star :size='36' :score='seller.score'></star>
            <span class="text" v-text="'('+seller.ratingCount+')'"></span>
            <span class="text" v-text="'月售'+seller.sellCount+'单'"></span>
          </div>
        </div>
        <div class="seller-food-info">
          <div class="qisong item border-r-1px">
            <div class="text">起送价</div>
            <div class="count">
              <span class="main-text">{{seller.minPrice}}</span>
              <small class="unit">元</small>
            </div>
          </div>
          <div class="peisong item border-r-1px">
            <div class="text">商家配送</div>
            <span class="count">
              <span class="main-text">{{seller.deliveryPrice}}</span>
              <small class="unit">元</small>
            </span>
          </div>
          <div class="pingjunpeisong item">
            <div class="text">平均配送时间</div>
            <div class="count">
              <span class="main-text">{{seller.deliveryTime}}</span>
              <small class="unit">分钟</small>
            </div>
          </div>
        </div>
        <div class="fav" @click='fav'>
          <span class="icon-favorite" :class='{active: isFav}'></span>
          <div class="text">{{isFav ? '已收藏':'收藏'}}</div>
        </div>
      </div>
      <div class="skip"></div>
      <div class="seller-tab-info">
        <div class="container">
          <div class="title">公告与活动</div>
          <div class="text">
            <p v-text="seller.bulletin"></p>
          </div>
          <ul class="bu-wrapper">
            <li class="bu-item border-t-1px" v-for="item in seller.supports">
              <icon :type='item.type' :icon-num='3'></icon>
              <span class="desc" v-text="item.description"></span>
            </li>
          </ul>
        </div>
      </div>
      <div class="skip"></div>
      <div class="pics">
        <div class="container">
          <div class="title">商家实景</div>
          <div class="img-container" ref="pics">
            <ul class='img-wrapper' ref="piclist">
              <li class="img-item" v-for="pic in seller.pics">
                <img class="img" :src="pic" alt="">
              </li>
            </ul>
          </div>
        </div>
      </div>
      <div class="skip"></div>
      <div class="seller-infos">
        <div class="container">
          <div class="title">商家信息</div>
          <ul>
            <li class="si-item border-t-1px" v-for="item in seller.infos" v-text="item"></li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import Star from '@/components/star/star'
import Icon from '@/components/icon/icon'

export default {
  props: {
    seller: {
      type: Object,
      default() {
        return {}
      }
    }
  },
  data() {
    return {
      // 默认没有收藏
      isFav: false
    }
  },
  methods: {
    // 收藏
    fav() {
      this.isFav = !this.isFav
    }
  },
  mounted() {
    if (this.seller.pics) {
      let picWidth = 120
      let margin = 6
      let width = (picWidth + margin) * this.seller.pics.length - margin
      this.$refs.piclist.style.width = width + 'px'
      this.$nextTick(() => {
        if (!this.picScroll) {
          this.picScroll = new BScroll(this.$refs.pics, {
            scrollX: true,
            eventPassthrough: 'vertical'
          })
        } else {
          this.picScroll.refresh()
        }
      })
    }
  },
  components: {
    Star,
    Icon
  }
}
</script>

<style lang="scss">
@import '../../common/sass/mixin.scss';

.seller-tab {
  .container {
    .header {
      position: relative;
      background-color: #fff;
      padding: 0 18px;
      .header-info {
        padding: 18px 0;
        @include border-b-1px(rgba(1, 17, 27, 0.1));
        .title {
          font-size: 14px;
          line-height: 14px;
          margin-bottom: 8px;
          color: rgb(7, 17, 27);
        }
        .star-wrapper {
          color: rgb(77, 85, 93);
          line-height: 18px;
          font-size: 0;
          .star {
            font-size: 10px;
            margin-right: 8px;
          }
          .text {
            display: inline-block;
            font-size: 10px;
            margin-right: 12px;
            &:last-child {
              margin-right: 0;
            }
          }
        }
      }
      .seller-food-info {
        display: flex;
        padding: 18px 0;
        .item {
          flex: 1;
          text-align: center;
          @include border-r-1px(rgba(7, 17, 27, 0.1));
          &:last-child {
            @include border-none();
          }
          .text {
            color: rgb(147, 153, 159);
            font-size: 10px;
            line-height: 10px;
            margin-bottom: 4px;
          }
          .count {
            font-size: 0;
            color: rgb(7, 17, 27);
            .main-text {
              font-size: 24px;
              line-height: 24px;
            }
            .unit {
              font-size: 10px;
            }
          }
        }
      }
      .fav {
        position: absolute;
        width: 50px;
        top: 18px;
        right: 18px;
        text-align: center;
        .icon-favorite {
          color: #d4d6d9;
          font-size: 24px;
          &.active {
            color: rgb(240, 20, 20);
          }
        }
        .text {
          margin-top: 4px;
          font-size: 10px;
          color: rgb(77, 85, 93);
        }
      }
    }
  }
  .seller-tab-info {
    .container {
      padding: 18px 18px 0 18px;
      .title {
        font-size: 14px;
        margin-bottom: 8px;
      }
      .text {
        font-size: 12px;
        color: rgb(240, 20, 20);
        line-height: 24px;
        font-weight: 200;
        padding: 0 12px 16px 12px;
      }
      .bu-wrapper {
        .bu-item {
          font-size: 0;
          padding: 16px 12px;
          @include border-t-1px(rgba(7, 17, 27, 0.1));
          .icon {
            display: inline-block;
            width: 16px;
            height: 16px;
            margin-right: 6px;
          }
          .desc {
            display: inline-block;
            font-size: 12px;
            height: 16px;
            line-height: 16px;
            color: rgb(7, 17, 27);
          }
        }
      }
    }
  }
  .pics {
    .container {
      padding: 18px;
      .title {
        font-size: 14px;
        color: rgb(7, 17, 27);
        line-height: 24px;
        margin-bottom: 12px;
      }
      .img-container {
        width: 100%;
        overflow: hidden;
        white-space: nowrap;
        .img-wrapper {
          font-size: 0;
          .img-item {
            display: inline-block;
            width: 120px;
            height: 90px;
            margin-right: 6px;
            &:last-child {
              margin-right: 0;
            }
            .img {
              width: 100%;
              height: 100%;
            }
          }
        }
      }
    }
  }
  .seller-infos {
    .container {
      padding: 18px 18px 0 18px;
      .title {
        font-size: 14px;
        color: rgb(7, 17, 27);
        line-height: 14px;
        margin-bottom: 12px;
      }
      .si-item {
        padding: 16px 12px;
        @include border-t-1px(rgba(7, 17, 27, 0.1));
        font-size: 12px;
        line-height: 16px;
        color: rgb(7, 17, 27);
      }
    }
  }
  .skip {
    height: 16px;
    background-color: #f3f5f7;
    border-bottom: 1px solid rgba(7, 17, 27, 0.1);
    border-top: 1px solid rgba(7, 17, 27, 0.1);
  }
}
</style>
