<template>
  <div class='goods'>
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <li :class="{'active':curIndex == index}" class=" menu-item" @click="selectMenu(index,$event)" v-for="(good,index) in goods ">
          <span class='text border-b-1px'>
            <icon v-show="good.type> 0" :type='good.type' :icon-num='2'></icon>{{good.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodsWrapper">
      <ul>
        <li v-for="item in goods" class="food-list-hook">
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
                <div class="cartcontrol-wrapper">
                  <cart-control :food='food'></cart-control>
                </div>
              </div>
            </dd>
          </dl>
        </li>
      </ul>
    </div>
    <shop-cart :select-foods='selectFoods' :delivery-price='seller.deliveryPrice' :min-price='seller.minPrice'></shop-cart>
  </div>
</template>

<script>
import Icon from '@/components/icon/icon'
import BScroll from 'better-scroll'
import ShopCart from '@/components/shopcart/shopcart'
import CartControl from '@/components/cartcontrol/cartcontrol'

const ERR_OK = 0
export default {
  props: {
    seller: {
      type: Object
    }
  },
  data() {
    return {
      goods: [],
      listHeight: [],
      scrollY: 0
    }
  },
  transitions: {},
  computed: {
    curIndex() {
      for (var i = 0, len = this.listHeight.length; i < len; i++) {
        let minh = this.listHeight[i]
        let maxh = this.listHeight[i + 1]
        if (!maxh || (this.scrollY >= minh && this.scrollY < maxh)) {
          return i
        }
      }
      return 0
    },
    // 此处可以优化, 因为每次都会遍历所有的数据
    selectFoods() {
      let foods = []
      this.goods.forEach((good) => {
        good.foods.forEach((food) => {
          if (food.count) {
            foods.push(food)
          }
        })
      })
      return foods
    }
  },
  created() {
    this.$http.get('/api/goods').then((res) => {
      res = res.body
      if (res.errno === ERR_OK) {
        this.goods = res.data
        // 数据绑定真正变化是在nextTick方法之后, 操作原生dom的时候一定确保dom都已经加载完毕
        this.$nextTick(() => {
          this._initScroll()
          this._calcFoodsHeight()
        })
      }
    })
  },
  methods: {
    // 定位右边菜的位置
    selectMenu(index, event) {
      if (!event._constructed) { return false }
      let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook')
      let el = foodList[index]
      this.foodsScroll.scrollToElement(el, 300)
    },
    _initScroll() {
      this.menuScroll = new BScroll(this.$refs.menuWrapper, {
        click: true
      })
      this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
        probeType: 3,
        click: true
      })
      this.foodsScroll.on('scroll', (pos) => {
        this.scrollY = Math.abs(Math.round(pos.y))
      })
    },
    _calcFoodsHeight() {
      var items = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook')
      for (var i = 0, len = items.length; i < len; i++) {
        this.listHeight.push(items[i].offsetTop)
      }
    }
  },
  components: {
    Icon,
    ShopCart,
    CartControl
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
      &.active {
        background-color: #fff;
        margin-top: -1px;
        .text {
          font-weight: 700;
          @include border-none();
        }
      }
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
          line-height: 12px;
          margin-top: 8px;
        }
        .sell-rating {
          font-size: 0;
          .sellcount {
            margin-right: 12px;
            font-size: 10px;
          }
          .rating {
            font-size: 10px;
          }
        }
        .price {
          margin-top: 4px;
          font-weight: 700;
          line-height: 16px;
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
        .cartcontrol-wrapper {
          position: absolute;
          right: 0;
          bottom: 12px;
        }
      }
    }
  }
}
</style>
