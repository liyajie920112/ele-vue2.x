<template>
  <div class='goods'>
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <li class="menu-item" v-for="good in goods">
          <span class='text border-b-1px'>
            <icon v-show="good.type > 0" :type='good.type' :icon-num='2'></icon>{{good.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodsWrapper">
      <ul>
        <li v-for="item in goods">
          <dl>
            <dt class="title" v-text="item.name"></dt>
            <dd class="food-item border-b-1px" v-for="food in item.foods">
              <div class="icon">
                <img width="57" height="57" :src="food.icon" alt="">
              </div>
              <div class="content">
                <div class="name" v-text="food.name"></div>
                <div class="desc" v-text="food.description"></div>
                <div class="sell-rating">
                  <span class="sellcount" v-text="'月售'+food.sellCount+'份'"></span>
                  <span class="rating" v-text="'好评率'+food.rating+'%'"></span>
                </div>
                <div class="price">
                  <span class="cur-price">
                    <span class="price-type">￥</span>{{food.price}}
                  </span>
                  <span class="old-price" v-if="food.oldPrice" v-text="'￥'+food.oldPrice"></span>
                </div>
              </div>
            </dd>
          </dl>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import Icon from '@/components/icon/icon'
import BScroll from 'better-scroll'

const ERR_OK = 0
export default {
  props: {
    seller: {
      type: Object
    }
  },
  data() {
    return {
      goods: []
    }
  },
  created() {
    this.$http.get('/api/goods').then((res) => {
      res = res.body
      console.log(res)
      if (res.errno === ERR_OK) {
        this.goods = res.data
        this.$nextTick(() => {
          this._initScroll()
        })
      }
    })
  },
  methods: {
    _initScroll() {
      this.menuScroll = new BScroll(this.$refs.menuWrapper, {})
      this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {})
    }
  },
  components: {
    Icon
  }
}
</script>

<style lang="scss">
@import '../../common/sass/mixin.scss';

.goods {
  display: flex;
  position: absolute;
  top: 174px;
  bottom: 46px;
  width: 100%;
  overflow: hidden;
  .menu-wrapper {
    flex: 0 0 80px;
    width: 80px; // 为了兼容android浏览器
    background-color: #f3f5f7;
    .menu-item {
      display: table;
      height: 54px;
      width: 56px;
      line-height: 14px;
      padding: 0 12px;
      .text {
        display: table-cell;
        width: 56px;
        vertical-align: middle;
        font-size: 12px;
        @include border-b-1px(rgba(7, 17, 27, 0.1));
        .icon {
          margin-right: 2px;
        }
      }
    }
  }
  .foods-wrapper {
    flex: 1;
    .title {
      height: 26px;
      line-height: 26px;
      font-size: 12px;
      color: rgb(147, 153, 159);
      background-color: #f3f5f7;
      border-left: 2px solid #d9dde1;
      padding-left: 14px;
    }
    .food-item {
      display: flex;
      margin: 18px 18px 0 18px;
      padding-bottom: 18px;
      @include border-b-1px(rgba(7, 17, 27, 0.1));
      &:last-child {
        @include border-none();
      }
      .icon {
        flex: 0 0 57px;
        margin-right: 10px;
      }
      .content {
        flex: 1;
        .name {
          font-size: 14px;
          color: rgb(7, 17, 27);
          line-height: 14px;
          margin-top: 2px;
        }
        .desc,
        .sell-rating {
          font-size: 10px;
          color: rgb(147, 153, 159);
          line-height: 10px;
          margin-top: 8px;
        }
        .sell-rating {
          font-size: 0;
          .sellcount {
            margin-right: 12px;
            font-size: 10px;
          }
          .rating{
            font-size: 10px;
          }
        }
        .price {
          margin-top: 8px;
          font-weight: 700;
          line-height: 24px;
          font-size: 0;
          .cur-price {
            color: rgb(240, 20, 20);
            margin-right: 8px;
            font-size: 14px;
            .price-type {
              font-size: 10px;
            }
          }
          .old-price {
            text-decoration: line-through;
            font-size: 10px;
            color: rgb(147, 153, 159);
          }
        }
      }
    }
  }
}
</style>
