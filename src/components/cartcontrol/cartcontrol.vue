<template>
  <div class="cartcontrol">
  	<transition name="cart-move">
  		<div class="cart-decrease" v-show="food.count>0" @click.stop="decreaseFood">
  			<span class="inner">-</span>
  		</div>
  	</transition>
  	<div class="cart-count" v-show="food.count>0">{{food.count}}</div>
  	<div class="cart-add" @click.stop="addFood($event)">+</div>
  </div>
</template>

<script type="text/ecmascript-6">
	import Vue from 'vue';
	export default{
		props: {
			food: {
				type: Object
			}
		},
		methods:{
			addFood(event){
				if(!event._constructed){
	 				return;
	 			}
				if(!this.food.count){
					Vue.set(this.food,'count',1)
				}else {
					this.food.count++;
				}
				this.$emit('cartadd',event.target);
			},
			decreaseFood(event){
				if(!event._constructed){
	 				return;
	 			}
	 			this.food.count--;
			}
		}
	};
</script>

<style>
.cart-move-enter-active, .cart-move-leave-active {
  opacity:1;
  transform: translate3d(0,0,0);
}
.cart-move-enter-active .inner, .cart-move-leave-active .inner{
	transform:rotate(0);
}
.cart-move-enter, .cart-move-leave{
  opacity: 0;
  transform: translate3d(24px,0,0);
}
.cart-move-enter .inner, .cart-move-leave-active .inner{
	transform:rotate(180deg);
}
.cartcontrol{
	font-size: 0;
}
.cartcontrol .cart-decrease{
	display: inline-block;
	vertical-align: top;
}
.cartcontrol .cart-decrease .inner,.cartcontrol .cart-add{
	display: inline-block;
	font-size: 18px;
	border-radius:50%;	
	border-radius: 50%;
	border: 2px solid rgb(0,160,240);
	height: 18px;
	width: 18px;
	vertical-align: top;
	text-align: center;
	line-height: 16px;
	transition: all .4s linear;
}
.cartcontrol .cart-decrease{
	color: rgb(0,160,240);
	background-color: #fff;
	transition: all .4s linear;
}
.cartcontrol .cart-count{
	display: inline-block;
	font-size: 10px;
	width: 24px;
	text-align: center;
	color: rgb(147,153,159);
	line-height: 20px;
}
.cartcontrol .cart-add{
	background-color: rgb(0,160,240);
	color: #fff;
}
</style>
