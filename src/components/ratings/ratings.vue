<template>
  <div class='ratings' ref="rating">
    <div class="container">
      <div class="rate-header">
        <div class="rate-score border-r-1px">
          <div class="score" v-text="seller.score"></div>
          <div class='text'>综合评分</div>
          <div class="score-text">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="rate-info">
          <ul>
            <li class="info-item">
              <span class="title">服务态度</span>
              <star :size='36' :score='seller.serviceScore'></star>
              <span class="score" v-text="seller.serviceScore"></span>
            </li>
            <li class="info-item">
              <span class="title">商品评分</span>
              <star :size='36' :score='seller.foodScore'></star>
              <span class="score" v-text="seller.foodScore"></span>
            </li>
            <li class="info-item">
              <span class="title">送达时间</span>
              <span class="time">{{seller.deliveryTime}}分钟</span>
            </li>
          </ul>
        </div>
      </div>
      <div class="rate-content">
        <rate-control :tabActiveType='rateType' :active='hasContent' :tab='tab' :rates='ratings' @toggleContent='toggleContent' @toggleType='toggleType'></rate-control>
        <div class="rate-list">
          <ul>
            <li v-show='needShow(rate.text,rate.rateType)' class="rate-item" v-for="rate in ratings">
              <div class="container border-b-1px">
                <div class="img">
                  <img :src="rate.avatar" alt="">
                </div>
                <div class="content">
                  <div class="title">
                    <span class="text" v-text="rate.username"></span>
                    <span class="time">{{rate.rateTime |formatDate}}</span>
                  </div>
                  <div class="star-wrapper">
                    <star :size='24' :score='rate.score'></star>
                    <span v-show="rate.deliveryTime" v-text="rate.deliveryTime+'分钟送达'"></span>
                  </div>
                  <div class="desc" v-show="rate.text" v-text="rate.text"></div>
                  <div class="category" v-show="rate.recommend.length > 0">
                    <i :class='[{"icon-thumb_up":rate.rateType === 0},{"icon-thumb_down":rate.rateType === 1}]'></i>
                    <span class="cate-item" v-for="cate in rate.recommend" v-text="cate"></span>
                  </div>
                </div>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import Star from '@/components/star/star'
import RateControl from '@/components/ratecontrol/ratecontrol'

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
      hasContent: true,
      rateType: -1,
      ratings: [],
      tab: {
        all: {
          text: '全部',
          type: -1
        },
        good: {
          text: '满意',
          type: 0
        },
        bad: {
          text: '不满意',
          type: 1
        }
      }
    }
  },
  created() {
    this.$http.get('/api/ratings').then((data) => {
      if (!data.body.errno) {
        this.ratings = data.body.data
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.rating, {
              click: true
            })
          } else {
            this.scroll.refresh()
          }
        })
      }
    })
  },
  methods: {
    needShow(text, type) {
      if (this.hasContent && !text) {
        return false
      }
      if (this.rateType === -1) {
        return true
      } else {
        return this.rateType === type
      }
    },
    toggleContent(hasContent) {
      this.hasContent = hasContent
    },
    toggleType(type) {
      this.rateType = type
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
    Star,
    RateControl
  }
}
</script>

<style lang="scss">
@import '../../common/sass/mixin.scss';

.ratings {
  position: absolute;
  top: 174px;
  left: 0;
  bottom: 0;
  width: 100%;
  overflow: hidden;
  background-color: #f3f5f7;
  .rate-header {
    display: flex;
    padding: 18px 0;
    border-bottom: 1px solid rgba(7, 17, 27, 0.1);
    margin-bottom: 16px;
    background-color: #fff;
    .rate-score {
      flex: 0 1 137px;
      @include border-r-1px(rgba(7, 17, 27, 0.1));
      text-align: center;
      .score {
        font-size: 24px;
        color: rgb(255, 153, 0);
        height: 28px;
        margin-bottom: 6px;
      }
      .text {
        font-size: 12px;
        color: rgb(7, 17, 27);
        line-height: 12px;
        margin-bottom: 8px;
      }
      .score-text {
        color: rgb(147, 153, 159);
        font-size: 10px;
        line-height: 10px;
        margin-bottom: 6px;
      }
    }
    .rate-info {
      padding: 0 24px;
      .info-item {
        color: rgb(7, 17, 27);
        margin-bottom: 8px;
        font-size: 0;
        line-height: 18px;
        &:last-child {
          margin-bottom: 0;
        }
        .title {
          font-size: 12px;
          display: inline-block;
          margin-right: 12px;
        }
        .star {
          margin-right: 12px;
        }
        .score {
          font-size: 12px;
          color: rgb(255, 153, 0);
        }
        .time {
          font-size: 12px;
          color: rgb(147, 153, 159);
        }
      }
    }
  }
  .rate-content {
    background-color: #fff;
    .rate-item {
      overflow: hidden;
      padding: 0 18px;
      .container {
        padding: 18px 0;
        display: flex;
        @include border-b-1px(rgba(7, 17, 27, 0.1));
        .img {
          flex: 0 0 28px;
          width: 28px;
          margin-right: 12px;
          img {
            width: 100%;
            border-radius: 50%;
          }
        }
        .content {
          width: 100%;
          .title {
            display: flex;
            font-size: 10px;
            line-height: 12px;
            margin-bottom: 4px;
            justify-content: space-between;
            .text {
              color: rgb(7, 17, 27);
            }
            .time {
              color: rgb(147, 153, 159);
            }
          }
          .star-wrapper {
            font-size: 10px;
            color: rgb(147, 153, 159);
            line-height: 12px;
            .star {
              margin-right: 6px;
            }
          }
          .desc {
            font-size: 12px;
            color: rgb(7, 17, 27);
            line-height: 18px;
            margin-bottom: 8px;
          }
          .category {
            font-size: 0;
            [class^=icon-thumb_],
            [class*=icon-thumb_] {
              font-size: 12px;
              &.icon-thumb_up {
                color: rgb(0, 160, 220);
              }
              &.icon-thumb_down {
                color: rgb(183, 187, 191);
              }
            }
            .cate-item {
              display: inline-block;
              line-height: 16px;
              margin: 0 0 4px 8px; // max-width: 1
              padding: 0 6px;
              font-size: 9px;
              border: 1px solid rgba(7, 17, 27, 0.1);
              border-radius: 2px;
              color: rgb(147, 153, 159);
            }
          }
        }
      }
    }
  }
}
</style>
