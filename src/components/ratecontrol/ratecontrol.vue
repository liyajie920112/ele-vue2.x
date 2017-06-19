<template>
  <div class="ratecontrol">
    <div class="title">商品评价</div>
    <div class="rate-tab">
      <ul class="border-b-1px">
        <li class="tab-item good" :class='{active: tabActiveType == tab.all.type}' @click='toggleType(tab.all.type)'>
          <span v-text='tab.all.text || ""'></span>
          <sub class="count" v-text="rates.length"></sub>
        </li>
        <li class="tab-item good" :class='{active: tabActiveType == tab.good.type}' @click='toggleType(tab.good.type)'>
          <span v-text='tab.good.text || ""'></span>
          <sub class="count" v-text="goodCount"></sub>
        </li>
        <li class="tab-item bad" :class='{active: tabActiveType == tab.bad.type}' @click='toggleType(tab.bad.type)'>
          <span v-text='tab.bad.text || ""'></span>
          <sub class="count" v-text="badCount"></sub>
        </li>
      </ul>
    </div>
    <div class="has-content" @click='toggleContent'>
      <i class="icon-check_circle" :class='{active: active}'></i>
      <span class="mark-text">只看有内容的评价</span>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    // 默认显示全部
    tabActiveType: {
      type: Number,
      default: -1
    },
    // 默认只看有内容的
    active: {
      type: Boolean,
      default: true
    },
    tab: {
      type: Object,
      default() {
        return {
          all: {
            text: '全部',
            type: -1
          },
          good: {
            text: '推荐',
            type: 0
          },
          bad: {
            text: '吐槽',
            type: 1
          }
        }
      }
    },
    rates: {
      type: Array,
      default() {
        return []
      }
    }
  },
  data() {
    return {
    }
  },
  computed: {
    goodCount() {
      return this.rates.filter((item, index) => { return item.rateType === 0 }).length
    },
    badCount() {
      return this.rates.filter((item, index) => { return item.rateType === 1 }).length
    }
  },
  methods: {
    // 过滤有内容的评价
    toggleContent() {
      this.$emit('toggleContent', !this.active)
    },
    toggleType(type) {
      this.$emit('toggleType', type)
    }
  }
}
</script>

<style lang='scss'>
@import '../../common/sass/mixin.scss';

.ratecontrol {
  padding: 18px 18px 0 18px;
  background-color: #fff;
  border-bottom: 1px solid rgba(7, 17, 27, 0.1);
  border-top: 1px solid rgba(7, 17, 27, 0.1);
  .title {
    margin-bottom: 18px;
  }
  .rate-tab {
    ul {
      font-size: 0;
      @include border-b-1px(rgba(7, 17, 27, 0.1));
      padding-bottom: 18px;
      .tab-item {
        display: inline-block;
        font-size: 12px;
        padding: 8px 12px;
        color: rgb(77, 85, 93);
        border-radius: 2px;
        margin-right: 8px;
        &:last-child {
          margin-right: 0;
        }
        &.good {
          background-color: rgba(0, 160, 220, 0.2);
          &.active {
            color: #fff;
            background-color: rgb(0, 160, 220);
          }
        }
        &.bad {
          background-color: rgba(77, 85, 93, 0.2);
          &.active {
            color: #fff;
            background-color: rgb(77, 85, 93);
          }
        }
        .count {
          font-size: 10px;
        }
      }
    }
  }
  .has-content {
    padding: 12px 0;
    font-size: 0;
    color: rgb(147, 153, 159);
    .icon-check_circle {
      display: inline-block;
      font-size: 24px;
      line-height: 24px;
      margin-right: 4px;
      vertical-align: top;
      &.active {
        color: #00c850;
      }
    }
    .mark-text {
      display: inline-block;
      font-size: 12px;
      line-height: 24px;
    }
  }
}
</style>
