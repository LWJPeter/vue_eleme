<template>
 <div class="cartcontrol">
     <div class="cart-decrease icon-remove_circle_outline" @click.stop.prevent="decreaseCart" v-show="food.count>0"></div>
     <div class="cart-count" v-show="food.count>0"> {{food.count}}</div>
     <div class="cart-add icon-add_circle" @click.stop.prevent="addCart"></div>
 </div>
</template>

<script>
import Vue from 'vue';
 export default {
   data () {
     return {

     }
   },
   components: {

   },
   props:{
       food:{
           type:Object
       }
   },
   created(){
       //console.log(this.food);
   },
   methods:{
       addCart(event){
           if(!event._constructed){
               return;
           }
           console.log("addCart click");
           if(!this.food.count){
              // this.food.count=1;
              Vue.set(this.food,'count',1);
           }else{
               this.food.count++;
           }
       },
       decreaseCart(event){
            if(!event._constructed){
                return;
            }
            if(this.food.count){
                this.food.count--;
            }
       }
   }

   

 }
</script>

<style lang="stylus" scoped>
.cartcontrol
    font-size 0
    .cart-decrease, .cart-add
        display inline-block
        padding 6px
        font-size 24px
        line-height 24px
        color rgb(0,160,220)
    .cart-count
        display inline-block
        vertical-align top
        width 12px
        padding-top 6px
        line-height 24px
        text-align center
        font-size 10px
        color rgb(147,153,159)
    .cart-add
        display inline-block


 
</style>
