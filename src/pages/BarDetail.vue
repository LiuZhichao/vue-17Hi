<template>
  <div id="bar-detail">
    <v-header></v-header>
    <div class="detail" ref="detail">
      <div class="detail-wrapper">
        <div class="swiper-content">
          <div class="info">
            <div class="left clearfix">
              <div class="name">{{bar_detail.name}}</div>
              <div class="address">{{bar_detail.address}}</div>
              <div class="status">{{bar_detail.avilable ? '营业中' : '休息'}}</div>
            </div>
            <div class="right clearfix">
              <div class="distance">{{bar_detail.distance}}公里</div>
            </div>
          </div>
          <swiper :options="swiperOption" :not-next-tick="notNextTick" class="swiper" ref="swiper">
            <!-- slides -->
            <swiper-slide v-for="(image,index) in bar_detail.bar_images" :key="index">
              <div class="mask"></div>
              <img v-lazy="image" class="lazyload">
            </swiper-slide>
            <!-- Optional controls -->
            <div class="swiper-pagination"  slot="pagination"></div>
          </swiper>
        </div>
        <div class="desc">
          {{bar_detail.description}}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import {mapGetters} from 'vuex'
  import Header from '@/components/Header/Header'
  import { swiper, swiperSlide } from 'vue-awesome-swiper'
  export default {
    name: 'app',
    async created (){
      try{
        let {data} = await this.fetchData(this.$route.params.id);
        this.barDetail = data.data;
        this.bar_detail = this.barDetail.bar_details[0];
      } catch(e) {

      }
    },
    data(){
      return {
        barDetail:{},
        bar_detail:{},
        swiperImages:[],
        notNextTick: false,
        swiperOption: {
          // swiper optionss 所有的配置同swiper官方api配置
          grabCursor : true,
          setWrapperSize :true,
          pagination : '.swiper-pagination',
          paginationClickable :true,
          mousewheelControl : true,
          observeParents:true,
          // if you need use plugins in the swiper, you can config in here like this
          // 如果自行设计了插件，那么插件的一些配置相关参数，也应该出现在这个对象中，如下debugger
          debugger: true
        }
      }
    },
    methods: {
      fetchData(bar_id){
       return new Promise((resolve,reject) => {
         this.$http({
           method: 'post',
           url: this.API + '/v1/bar/barDetails',
           data: {
             bar_id: bar_id,
             ...this.geolocation
           }
         }).then((res) => {
           resolve(res);
         }).catch((e) => {
           reject(e.response);
         });
       });
      }
    },
    computed: {
      ...mapGetters(['API','geolocation'])
    },
    components: {
      "v-header": Header,
      swiper,
      swiperSlide
    },
    watch: {
      $route(to,from){
        if(to.name === 'BarDetail'){
          this.barDetail = {};
          this.bar_detail = {};
          this.fetchData(to.params.id).then((res) => {
            this.barDetail = res.data.data;
            this.bar_detail = this.barDetail.bar_details[0];
            this.$nextTick(() => {
              this.$refs['swiper'].swiper.slideTo(0,0);
            });
          });
        }
      }
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  #bar-detail
    position relative
    width 100%
    height 100%
    color #fff
    background #1b2227
    .detail-wrapper
      position absolute
      top 0
      left 0
      width 100%
      height 100%
      box-sizing border-box
      padding-top 44px
      z-index 0
      color #fff
      overflow hidden
      .swiper-content
        position relative
        width 100%
        box-sizing border-box
        padding-top 58%
        .info
          position absolute
          bottom 20px
          box-sizing border-box
          padding 0 10px
          z-index 6
          width 100%
          &>div
            float left
          .left
            width 75%
            .name
              font-weight 600
              font-size 18px
              line-height 22px
              margin-bottom 4px
            .address
              font-weight 500
              font-size 12px
              margin-bottom 8px
            .status
              color #e0e0e0
              font-size 10px
          .right
            text-align right
            width 25%
            .distance
              position absolute
              right 10px
              bottom 0
              font-size 12px
        .swiper
          position absolute
          top 0
          width 100%
          height 100%
          .swiper-wrapper
            width 100%
            height 100%
          .swiper-slide
            position relative
            width 100%
            height 100%
            .mask
              position absolute
              top 0
              left 0
              width 100%
              height 100%
              z-index 2
              background rgba(0,0,0,0.3)
            img
              width 100%
              height 100%
          .swiper-pagination-bullets
            bottom 4px
            .swiper-pagination-bullet
                background #fff!important
                opacity 0.6
                margin 0 4px!important
                width 6px
                height 6px
            .swiper-pagination-bullet-active
              opacity 1
      .desc{
        width 100%
        margin-top 24px
        box-sizing border-box
        padding 0 10px
        font-size 12px
        color #e0e0e0
        line-height 16px
      }
</style>
