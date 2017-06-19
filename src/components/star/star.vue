<template>
  <div class="star" :class='starType'>
    <span v-for="itemClass in itemClasses" :class="itemClass" class="star-item"></span>
  </div>
</template>

<script>
const CLS_LEN = 5
const CLS_ON = 'on'
const CLS_OFF = 'off'
const CLS_HALF = 'half'
export default {
  props: {
    size: {
      type: Number
    },
    score: {
      type: Number
    }
  },
  computed: {
    starType() {
      return 'star-' + this.size
    },
    itemClasses() {
      let result = []
      let score = Math.floor(this.score * 2) / 2 // 如果是4.3 结果就是4, 如果是4.6, 结果就是4.5
      let hasDecimal = score % 1 !== 0 // 如果不等于0, 则说明有小数
      let integer = Math.floor(score)
      for (var i = 0; i < integer; i++) {
        result.push(CLS_ON)
      }
      if (hasDecimal) {
        result.push(CLS_HALF)
      }
      while (result.length < CLS_LEN) {
        result.push(CLS_OFF)
      }
      return result
    }
  }
}
</script>

<style lang="scss">
@import '../../common/sass/mixin.scss';

.star {
  display: inline-block;
  vertical-align: top;
  &.star-24 {
    .star-item {
      display: inline-block;
      width: 10px;
      height: 10px;
      background-repeat: no-repeat;
      background-size: 10px 10px;
      margin-right: 3px;
      &:last-child {
        margin-right: 0;
      }
      &.on {
        @include bg-image('star24_on')
      }
      &.half {
        @include bg-image('star24_half')
      }
      &.off {
        @include bg-image('star24_off')
      }
    }
  }
  &.star-36 {
    .star-item {
      display: inline-block;
      width: 15px;
      height: 15px;
      background-repeat: no-repeat;
      background-size: 15px 15px;
      margin-right: 6px;
      &:last-child {
        margin-right: 0;
      }
      &.on {
        @include bg-image('star36_on')
      }
      &.half {
        @include bg-image('star36_half')
      }
      &.off {
        @include bg-image('star36_off')
      }
    }
  }
  &.star-48 {
    .star-item {
      display: inline-block;
      width: 20px;
      height: 20px;
      background-repeat: no-repeat;
      background-size: 20px 20px;
      margin-right: 22px;
      &:last-child {
        margin-right: 0;
      }
      &.on {
        @include bg-image('star48_on')
      }
      &.half {
        @include bg-image('star48_half')
      }
      &.off {
        @include bg-image('star48_off')
      }
    }
  }
}
</style>
