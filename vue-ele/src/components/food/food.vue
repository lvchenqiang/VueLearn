<template>
  <transition name="move">
    <div v-show="showFlag" class="food" ref="fooddetail">
      <div class="foodcontent">
        <div class="image-header">
          <img :src="food.image">
          <div @click="closedetail" class="backIcon">
            <i class="icon-arrow_lift"></i>
          </div>

        </div>
        <div class="content">
          <h1 class="title">{{food.name}}</h1>
          <div class="detail">
            <span class="sell-count">月售:{{food.sellCount}}份</span><span class="rating">好评率:{{food.rating}}% </span>
          </div>
          <div class="price">
            <span class="nowprice">￥{{food.price}}</span><span class="oldprice" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
          </div>

          <div class="cartcontroller-wrapper">
            <v-cartcontroller :food="food"></v-cartcontroller>
          </div>
          <transition name="fade">
            <div @click.stop.prevent="addfirstfood(food,$event)" class="buy" v-show="!food.count || food.count===0">
              加入购物车
            </div>
          </transition>
        </div>
        <v-Split v-show="food.info"></v-Split>
        <div class="info" v-show="food.info">
          <h1 class="title">商品信息</h1>
          <p class="text">{{food.info}}</p>
        </div>

        <v-Split v-show="food.info"></v-Split>
        <div class="rating">
          <h1 class="title">商品评价</h1>
          <v-ratingselect @toggleconent="toggleConent" @select="selectRating" :select-type="selectType"
                          :onlyContent="onlyContent" :desc="desc"
                          :ratings="food.ratings"></v-ratingselect>


          <div class="ratings-wrapper">
            <ul v-show="food.ratings && food.ratings.length">
              <li v-show="needshow(rating.rateType,rating.text)" v-for="rating in food.ratings"
                  class="rating-item border-1px">
                <div class="user">
                  <span class="username">{{rating.username}}</span>
                  <img class="avatar" width="12" height="12" :src="rating.avatar">
                </div>
                <div class="time">{{rating.rateTime | formatDate}}</div>
                <p class="text">
                  <span :class="{'icon-thumb_up':rating.rateType==0,'icon-thumb_down':rating.rateType==1}"></span>
                  {{rating.text}}
                </p>
              </li>
            </ul>
            <div class="no-ratings" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
          </div>

        </div>

      </div>
    </div>

  </transition>
</template>


<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import cartcontroller from '../cartcontroller/cartcontroller.vue';
  import Vue from 'vue';
  import Split from '../split/split.vue';
  import ratingselect from '../ratingselect/ratingselect.vue';
  import {formatDate} from '../../common/js/date';
  //  const POSITIVE = 0;
  //  const NEGATIVE = 1;
  const ALL = 2;
  export default {
    props: {
      food: {
        type: Object
      }
    },
    data() {
      return {
        showFlag: false,
        selectType: ALL,
        onlyContent: true,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      };
    },
    methods: {
      show() {
        this.showFlag = true;
        this.selectType = ALL;
        this.onlyContent = false;
        this.$nextTick(() => {
          if (!this.scrool) {
            this.scrool = new BScroll(this.$refs.fooddetail, {
              click: true
            });
          } else {
            this.scrool.refresh();
          }
        });
      },
      closedetail() {
        this.showFlag = false;
      },
      addfirstfood(food, event) {
        console.log(event);
        this.$emit('add', event.target);
        Vue.set(this.food, 'count', 1);
      },
      selectRating (type) {
        this.selectType = type;
        this.$nextTick(() => {
          this.scrool.refresh();
        });
      },
      toggleConent () {
        this.onlyContent = !this.onlyContent;
        this.$nextTick(() => {
          this.scrool.refresh();
        });
      },
      needshow(type, content) {
        if (this.onlyContent && !content) {
          return false;
        }
        if (this.selectType === ALL) {
          return true;
        } else {
          return this.selectType === type;
        }
      }
    },
    components: {
      'v-cartcontroller': cartcontroller,
      'v-Split': Split,
      'v-ratingselect': ratingselect
    },
    filters: {
      formatDate (time) {
        let date = new Date(time);
        return formatDate(date, 'yyyy-MM-dd hh:mm');
      }
    }
  };
</script>


<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl";
  .food
    position fixed
    left 0
    top 0
    bottom 48px
    z-index 30
    width 100%
    background-color white
    transform: translate3d(0, 0, 0)
    &.move-enter-active, &.move-leave-active
      transition: all 0.2s linear
    &.move-enter, &.move-leave-active
      transform: translate3d(100%, 0, 0)
    .image-header
      position relative
      width 100%
      height 0
      padding-bottom 100%
      img
        position absolute
        top 0
        left 0
        width 100%
        height 100%
      .backIcon
        position absolute
        top 10px
        left 0
        .icon-arrow_lift
          display block
          padding 10px
          font-size 20px
          color white
    .content
      padding 18px
      position relative
      .title
        line-height 14px
        margin-bottom 8px
        font-size 14px
        font-weight 700
        color rgb(7, 17, 27)
      .detail
        margin-bottom 18px
        line-height 10px
        height 10px
        font-size 0
        .sell-count, .rating
          font-size 10px
          color rgb(147, 153, 159)
        .sell-count
          margin-right 12px
      .price
        font-weight: 700
        line-height: 24px
        .nowprice
          margin-right: 10px
          font-size: 14px
          color: rgb(240, 20, 20)
        .oldprice
          text-decoration: line-through
          font-size: 10px
          color: rgb(147, 153, 159)

      .cartcontroller-wrapper
        position absolute
        right 12px
        bottom 12px
      .buy
        position absolute
        right 18px
        bottom 18px
        z-index 10
        line-height 24px
        height 24px
        padding 0 12px
        box-sizing border-box
        border-radius 12px
        font-size 10px
        color white
        background-color rgb(0, 160, 220)
        opacity: 1
        &.fade-enter-active, &.fade-leave-active
          transition: all 0.2s
        &.fade-enter, &.fade-leave-active
          opacity: 0
          z-index: -1

    .info
      padding 18px
      .title
        line-height 14px
        margin-bottom 6px
        font-size 14px
        font-weight 700
        color rgb(7, 17, 27)
      .text
        line-height 24px
        padding 0 8px
        font-size 12px
        color rgb(77, 85, 93)

    .rating
      padding-top 18px
      .title
        line-height 14px
        margin-left 18px
        font-size 14px
        font-weight 700
        color rgb(7, 17, 27)
      .ratings-wrapper
        padding 0 18px
        .rating-item
          position relative
          padding 16px 0
          border-1px(rbga(17, 27, 37, 0.1))
          .user
            position absolute
            right 0
            top 16px
            font-size 0
            line-height 12px
            .username
              display inline-block
              vertical-align top
              margin-right 6px
              font-size 10px
              color rgb(147, 153, 159)
            .avatar
              border-radius 50%

          .time
            margin-bottom 6px
            line-height 12px
            font-size 10px
            color rgb(147, 153, 159)
          .text
            line-height 16px
            font-size 12px
            color rgb(7, 17, 27)
            .icon-thumb_up, .icon-thumb_down
              line-height 24px
              margin-right 4px
              font-size 12px
            .icon-thumb_up
              color rgb(0, 160, 220)
            .icon-thumb_down
              color rgb(147, 153, 159)

        .no-ratings
          padding 16px 0
          font-size 12px
          color rgb(147, 153, 159)
</style>

