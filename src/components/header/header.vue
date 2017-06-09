<template>
  <div class='header'>
    <div class="content-wrapper">
      <div class="avatar">
        <img src="" width="64" height="64" :src='seller.avatar' alt="">
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name" v-text="seller.name"></span>
        </div>
        <div class="description">
          {{seller.description}} / {{seller.deliveryTime}}分钟送达
        </div>
        <div v-if="seller.supports" class="support">
          <icon :type='seller.supports[0].type' :icon-num='0'></icon>
          <span class="text" v-text="seller.supports[0].description"></span>
        </div>
      </div>
      <div @click='showDetail' v-if="seller.supports" class="support-count">
        <span class="count" v-text="seller.supports.length + '个'"></span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="bulletin-wrapper" @click='showDetail'>
      <span class="bulletin-title"></span>
      <span class="bulletin-text" v-text="seller.bulletin"></span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="background">
      <img :src="seller.avatar" width="100%" height="100%" alt="">
    </div>
    <transition name="fade">
      <div v-show="detailShow" class="detail">
        <div class="detail-wrapper clearfix">
          <div class="detail-main">
            <h1 class="name" v-text="seller.name"></h1>
            <div class="star-wrapper">
              <star :size='48' :score='seller.score'></star>
            </div>
            <detail-title :title="'优惠信息'"></detail-title>
            <ul class="supports">
              <li class="support" v-for="support in seller.supports">
                <icon :type='support.type' :icon-num='1'></icon>
                <span class="desc" v-text="support.description"></span>
              </li>
            </ul>
            <detail-title :title="'商家公告'"></detail-title>
            <div class="bulletin">
              <p class="content" v-text="seller.bulletin"></p>
            </div>
          </div>
        </div>
        <div class="detail-close">
          <i class="icon-close" @click='hideDetail'></i>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import Star from '@/components/star/star'
import Title from '@/components/title/title'
import Icon from '@/components/icon/icon'

export default {
  props: {
    seller: {
      type: Object
    }
  },
  data() {
    return {
      detailShow: false
    }
  },
  methods: {
    showDetail() {
      this.detailShow = true
    },
    hideDetail() {
      this.detailShow = false
    }
  },
  components: {
    Star,
    DetailTitle: Title,
    Icon
  }
}
</script>

<style lang='scss'>
@import '../../common/sass/mixin.scss';

.header {
  position: relative;
  background-color: rgba(7, 17, 27, 0.5);
  color: #fff;
  overflow: hidden;
  .content-wrapper {
    position: relative;
    padding: 24px 12px 18px 24px;
    font-size: 0;
    .avatar {
      display: inline-block;
      vertical-align: top;
      img {
        border-radius: 2px;
      }
    }
    .content {
      display: inline-block;
      margin-left: 16px;
      font-size: 14px;
      .title {
        margin: 2px 0 8px 0;
        .brand {
          display: inline-block;
          width: 30px;
          height: 18px;
          @include bg-image('brand');
          background-size: 30px 18px;
          background-repeat: no-repeat;
          vertical-align: top;
        }
        .name {
          font-weight: bold;
          font-size: 16px;
          margin-left: 6px;
          line-height: 18px;
        }
      }
      .description {
        font-size: 12px;
        line-height: 12px;
        margin-bottom: 10px;
      }
      .support {
        font-size: 0;
        .text {
          line-height: 12px;
          font-size: 10px;
          margin-left: 4px;
        }
      }
    }
    .support-count {
      position: absolute;
      right: 12px;
      bottom: 18px;
      height: 24px;
      padding: 0 8px;
      font-size: 10px;
      line-height: 24px;
      background-color: rgba(0, 0, 0, 0.2);
      border-radius: 12px;
      font-size: 0;
      .count {
        margin-right: 2px;
        vertical-align: top;
        font-size: 10px;
      }
      .icon-keyboard_arrow_right {
        font-size: 10px;
        line-height: 24px;
      }
    }
  }
  .bulletin-wrapper {
    position: relative;
    height: 28px;
    line-height: 28px;
    padding: 0 24px 0 12px;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    background-color: rgba(7, 17, 27, 0.2);
    .bulletin-title {
      display: inline-block;
      width: 22px;
      height: 12px;
      vertical-align: top;
      margin-top: 8px;
      @include bg-image('bulletin');
      background-size: 22px 12px;
      background-repeat: no-repeat;
    }
    .bulletin-text {
      vertical-align: top;
      font-size: 10px;
      margin: 0 4px;
    }
    .icon-keyboard_arrow_right {
      position: absolute;
      font-size: 10px;
      right: 12px;
      top: 8px;
    }
  }
  .background {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: -1;
    filter: blur(10px);
  }
  .detail {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 100;
    overflow: auto;
    backdrop-filter: blur(10px);
    background-color: rgba(7, 17, 27, 0.8);
    &.fade-enter-active,
    &.fade-leave-active {
      opacity: 1;
      transition: all 1s;
    }

    &.fade-enter,
    &.fade-leave-active {
      opacity: 0;
    }
    .detail-wrapper {
      min-height: 100%;
      width: 100%;
      .detail-main {
        margin-top: 64px;
        padding-bottom: 64px;
        .name {
          margin-top: 64px;
          text-align: center;
          line-height: 16px;
          font-size: 16px;
          font-weight: 700;
        }
        .star-wrapper {
          margin-top: 16px;
          text-align: center;
        }
        .supports {
          width: 80%;
          list-style: none;
          margin: 0 auto;
          padding: 0;
          .support {
            height: 16px;
            margin-bottom: 12px;
            padding: 0 12px;
            font-size: 0;
            &:last-child {
              margin-bottom: 0;
            }

            .desc {
              margin-left: 6px;
              font-size: 12px;
              line-height: 16px;
            }
          }
        }
        .bulletin {
          width: 80%;
          margin: 0 auto;
          .content {
            padding: 0 12px;
            font-size: 12px;
            line-height: 24px;
          }
        }
      }
    }
    .detail-close {
      position: relative;
      width: 32px;
      height: 32px;
      margin: -64px auto 0 auto;
      clear: both;
      font-size: 32px;
    }
  }
}
</style>
