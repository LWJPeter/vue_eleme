<template>
 <div v-show="showFlag" class="food" ref="food" >
     <div class="food-content">
         <div class="image-header">
             <img :src="food.image" alt="图片">
             <div class="back" @click="hide">
                 <i class="icon-arrow_lift"></i>
             </div>
        </div>
        <div class="content">
            <div class="title">{{food.name}}</div>
            <div class="detail">
                <span class="sell-count">月售{{food.sellCount}}份</span>
                <span class="rating">好评率{{food.rating}}%</span>
            </div>
            <div class="price">
                <span class="now">￥{{food.price}}</span>
                <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
            </div>
            <div class="cartcontrol-wrapper">
            <Cartcontrol :food="food"></Cartcontrol>
            </div>
            <div @click.stop.prevent="addFirst($event)" class="buy" 
            v-show="!food.count || food.count===0">加入购物车</div>
        </div>
        <Split  v-show="food.info"></Split>
        <div class="info" v-show="food.info">
            <h1 class="title">商品信息</h1>
            <p class="text">{{food.info}}</p>
        </div>
        <Split></Split>
        <div class="rating">
            <h1 class="title">商品评价</h1>
            <div class="ratingselect-wrapper">
                <Ratingselect :ratings="food.ratings" :desc="desc" :selectType="selectType"  
                :onlyContent="onlyContent" @ratingtypeSelect="ratingtypeSelect"  @contentToggle="contentToggle" ></Ratingselect>
                <div class="rating-wrapper">
                    <ul v-show="food.ratings && food.ratings.length">
                        <li v-show="needShow(rating.rateType,rating.text)" v-for="(rating,index) in food.ratings" :key="index" class="rating-item">
                            <div class="user">
                                <span class="name">{{rating.username}}</span>
                                <img class="avatar" :src="rating.avatar" width="12" height="12">
                            </div>
                            <div class="time">{{rating.rateTime }}</div>
                            <p class="text">
                                <span :class="{'icon-thumb_up':rating.rateType === 0,'icon-thumb_down':rating.rateType === 1}"></span>
                                {{rating.text}}
                            </p>
                        </li>
                    </ul>
                    <div class="no-rating" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
                </div>
            </div>
        </div>
     </div>
 </div>
</template>

<script>
import Vue from "vue";
import BScroll from "better-scroll";
import { formatDate } from "./../../common/js/date";
import Cartcontrol from "./../cartcontrol/Cartcontrol";
import Ratingselect from "./../ratingselect/Ratingselect";
import Split from "./../split/Split";
const POSITIVE = 0;
const NEGATIVE = 1;
const ALL = 2;

 export default {
   data () {
     return {
         showFlag:false,
         selectType:ALL,
         onlyContent: true,
         desc:{
             all:"全部",
             positive:"推荐",
             negative:"吐槽"
         }
     }
   },
   components: {
       Cartcontrol,
       Split,
       Ratingselect
   },
   props:{
       food:{
           type:Object
       }
   },
//    event:{
//        'ratingtype.select'(type){
//            this.selectType=type;
//        },
//        'content.tottle'(onlyContent){
//            this.onlyContent=onlyContent;
//        }
//    },
    filters:{
        formatDate(time){
            let date= new Date(time);
            return formatDate(date,'yyyy-MM-dd hh:mm');
        }
    },
   methods:{
       ratingtypeSelect(type){
           console.log("ratingtypeSelect",type);
           this.selectType=type;
           this.$nextTick(()=>{
                this.bscroll.refresh();
           })
           
       },
       contentToggle(onlyContent){
           console.log("contentToggle",onlyContent);
           this.onlyContent=onlyContent;
           this.$nextTick(()=>{
                this.bscroll.refresh();
           })
       },
       needShow(type,text){
           if(this.onlyContent && !text){
               return false;
           }
           if(this.selectType === ALL){
               return true;
           }else {
               return type === this.selectType;
           }
       },
       show(){
           this.showFlag=true;
           this.selectType=ALL;
           this.onlyContent=true;
           //什么时候初始化BScroll
           this.$nextTick(()=>{
               if(!this.bscroll){
                   this.bscroll=new BScroll(this.$refs.food,{click:true});
               }else{
                   this.bscroll.refresh();
               }
           });
       },
       hide(){
           this.showFlag=false;
       },
       addFirst(event){
           if(!event._constructed){
               return;
           } 
           Vue.set(this.food,"count" , 1);
       }
   }
 }
</script>

<style lang="stylus" scoped>
@import '../../common/stylus/mixin'

.food
    position fixed
    left 0
    top 0
    bottom 48px
    z-index 30
    width 100%
    background #ffffff
    .image-header
        position relative
        width 100%
        height 0
        padding-top 100%
        img 
            position absolute
            top 0
            left 0
            width 100%
            height 100%
        .back
            position absolute
            top 10px
            left 0
            .icon-arrow_lift
                display block
                padding 10px
                font-size 20px
                color #ffffff
    .content
        position relative
        padding 18px
        .title
            line-height 14px
            margin-bottom 8px
            font-size 14px
            font-weight bold
            color rgb(7,17,27)
        .detail
            margin-bottom 18px
            line-height 10px
            font-size 0
            height 10px
            .sell-count,.rating
                font-size 10px
                color rgb(147,153,159)
            .sell-count
                margin-right 12px
        .price
            font-weight bold
            line-height 24px
            .now
                margin-right 8px
                font-size 14px
                color rgb(240,20,20)
            .old
                text-decoration line-through
                font-size 10px
                color rgb(147,153,159)
        .cartcontrol-wrapper
            position absolute
            right 12px
            bottom 12px
        .buy
            position absolute
            right 18px
            bottom 18px
            z-index 10
            height 24px
            line-height 24px
            padding 0 12px
            box-sizing border-box
            border-radius 12px
            font-size 10px
            color #ffffff
            background rgb(0,160,260)
    .info
        padding 18px
        .title
            line-height 14px
            margin-bottom 6px
            font-size 14px
            color rgb(7,17,27)
        .text
            line-height 24px
            padding 0 8px
            font-size 14px
            color rgb(77,85,93)
    .rating
        padding-top 18px
        .title
            line-height 14px
            margin-left 18px
            font-size 14px
            color rgb(7,17,27)
        .rating-wrapper
            padding  0 18px
            .rating-item
                position relative
                padding 16px 0
                border-1px(rgba(7,17,27,0.1))
                .user
                    position absolute
                    right 0
                    top 16px
                    line-height 12px
                    font-size 0
                    .name
                        display inline-block
                        margin-right 6px
                        vertical-align top
                        font-size 10px
                        color rgb(146,153,159)
                    .avatar
                        border-radius 50%
                .time
                    margin-bottom 6px
                    line-height 12px
                    font-size 10px
                    color rgb(147,153,159)
                .text
                    line-height 16px
                    font-size 12px
                    color rgb(7,17,27)
                    .icon-thumb_up,.icon-thumb_down
                        margin-right 4px
                        line-height 24px
                        font-size 12px
                    .icon-thumb_up
                        color rgb(0,160,220)
                    .icon-thumb_down
                        color rgb(147,153,159)
            .no-rating
                padding 16px 0
                font-size 12px
                color rgb(147,153,159)








         


                




 
</style>
