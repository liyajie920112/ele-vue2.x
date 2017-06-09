<template>
  <span class="icon" :class='[classMap[type],iconMap[iconNum]]'>
  </span>
</template>
<script>
export default {
  props: {
    type: {
      type: Number
    },
    iconNum: {
      type: Number
    }
  },
  created() {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    this.iconMap = ['icon-1', 'icon-2', 'icon-3', 'icon-4']
  }
}
</script>
<style lang="scss" scoped>
@import '../../common/sass/mixin.scss';
.icon {
  display: inline-block;
  vertical-align: top;
  background-repeat: no-repeat;
  &.icon-1 {
    width: 12px;
    height: 12px;
    background-size: 12px 12px;
    &.decrease {
      @include bg-image('decrease_1');
    }
    &.discount {
      @include bg-image('discount_1');
    }
    &.guarantee {
      @include bg-image('guarantee_1');
    }
    &.invoice {
      @include bg-image('invoice_1');
    }
    &.special {
      @include bg-image('special_1');
    }
  }

  &.icon-2 {
    width: 16px;
    height: 16px;
    background-size: 16px 16px;
    &.decrease {
      @include bg-image('decrease_2');
    }
    &.discount {
      @include bg-image('discount_2');
    }
    &.guarantee {
      @include bg-image('guarantee_2');
    }
    &.invoice {
      @include bg-image('invoice_2');
    }
    &.special {
      @include bg-image('special_2');
    }
  }
  &.icon-3 {
    width: 12px;
    height: 12px;
    background-size: 12px 12px;
    &.decrease {
      @include bg-image('decrease_3');
    }
    &.discount {
      @include bg-image('discount_3');
    }
    &.guarantee {
      @include bg-image('guarantee_3');
    }
    &.invoice {
      @include bg-image('invoice_3');
    }
    &.special {
      @include bg-image('special_3');
    }
  }
  &.icon-4 {
    width: 16px;
    height: 16px;
    background-size: 16px 16px;
    &.decrease {
      @include bg-image('decrease_4');
    }
    &.discount {
      @include bg-image('discount_4');
    }
    &.guarantee {
      @include bg-image('guarantee_4');
    }
    &.invoice {
      @include bg-image('invoice_4');
    }
    &.special {
      @include bg-image('special_4');
    }
  }
}
</style>
