<template>
  <div class="slider" ref="refslider">
    <div class="slider-group" ref="refsliderGroup">
      <slot>

      </slot>
    </div>
    <div class="dots">
      <span class="dot" :class="{active: currentPageIndex === index }" v-for="(item,index) in dots"></span>
    </div>
  </div>
</template>
<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import {addClass} from 'common/js/dom';
  export default {
    name: 'slider',
    props: {
      loop: {
        type: Boolean,
        default: true
      },
      autoPlay: {
        type: Boolean,
        default: true
      },
      interval: {
        type: Number,
        default: 2500
      }
    },
    data() {
      return {
        dots: [],
        currentPageIndex: 0
      };
    },
    mounted() {
      setTimeout(() => {
        this._setSliderWidth();
        this._initDots();
        this._initSlider();
        if (this.autoPlay) {
          console.log('mounted 12312312');
          this._play();
        }
      }, 20);

      window.addEventListener('resize', () => {
        if (!this.sliderScroll) {
          return;
        }
        this._setSliderWidth(true);
        this.sliderScroll.refresh();
      });
    },
    activated() {
        console.log('activated23123');
      if (this.autoPlay) {
        this._play();
      }
    },
    deactivated() {
      clearTimeout(this.timer);
    },
    beforeDestroy() {
      clearTimeout(this.timer);
    },
    methods: {
      _setSliderWidth(isResize) {
        this.children = this.$refs.refsliderGroup.children;
        let width = 0;
        let sliderWidth = this.$refs.refslider.clientWidth;
        for (let i = 0; i < this.children.length; i++) {
          let child = this.children[i];
          addClass(child, 'slider-item');

          child.style.width = sliderWidth + 'px';
          width += sliderWidth;
        }
        if (this.loop && !isResize) {
          width += 2 * sliderWidth;
        }
        this.$refs.refsliderGroup.style.width = width + 'px';
      },
      _initSlider() {
        this.sliderScroll = new BScroll(this.$refs.refslider, {
          scrollX: true,
          scrollY: false,
          momentum: false,
          snap: true,
          snapLoop: this.loop,
          snapThreshold: 0.3,
          snapSpeed: 400,
          click: true
        });
        this.sliderScroll.on('scrollEnd', () => {
          let pageIndex = this.sliderScroll.getCurrentPage().pageX;
          if (this.loop) {
            pageIndex -= 1;
          }
          this.currentPageIndex = pageIndex;
          if (this.autoPlay) {
            clearTimeout(this.timer);
            this._play();
          }
        });
//        this.sliderScroll.on('beforeScrollStart', () => {
//          if (this.autoPlay) {
//            clearTimeout(this.timer);
//          }
//        });
      },
      _initDots() {
        this.dots = new Array(this.children.length);
      },
      _play() {
        let pageIndex = this.currentPageIndex + 1;
        if (this.loop) {
          pageIndex += 1;
        }
        this.timer = setTimeout(() => {
          this.sliderScroll.goToPage(pageIndex, 0, this.interval);
        }, this.interval);
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/variable.styl";
  .slider
    min-height 1px
    .slider-group
      position relative
      overflow hidden
      white-space nowrap
      .slider-item
        float: left
        box-sizing: border-box
        overflow: hidden
        text-align: center
        a
          display: block
          width: 100%
          overflow: hidden
          text-decoration: none
        img
          display: block
          width: 100%
    .dots
      position absolute
      right 0
      left 0
      bottom 12px
      text-align center
      font-size 0
      .dot
        display inline-block
        margin 0 4px
        width 8px
        height 8px
        border-radius 50%
        background-color $color-text-l
        &.active
          width 20px
          border-radius 5px
          background-color $color-text-ll


</style>
