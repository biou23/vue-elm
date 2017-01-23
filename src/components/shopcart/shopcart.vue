<template>
	<div>
		<div class="shopcart">
			<div class="content" @click="toggleList">
				<div class="content-left">
					<div class="logo-wrapper">
						<div class="logo" :class="{'highlight':totalCount>0}"></div>
						<div class="num" v-show="totalCount>0">{{totalCount}}</div>
					</div>
					<div class="price" :class="{'highlight':totalCount>0}">
						￥{{totalPrice}}元
					</div>
					<div class="desc">
						另需配送费{{deliveryPrice}}元
					</div>
				</div>
				<div class="content-right">
					<div class="pay" v-show="totalPrice==0">
						￥{{minPrice}}元起送
					</div>
					<div class="pay" v-show="totalPrice>0&&totalPrice<minPrice">
						还差￥{{minPrice-totalPrice}}元起送
					</div>
					<div class="pay pay-ok" v-show="totalPrice>=minPrice">
						去结算
					</div>
				</div>
			</div>
			<div class="ball-container">
				<transition-group name="cart-yuan" tag="div" v-on:before-enter="beforeEnter" v-on:enter="enter" v-on:after-enter="afterEnter">
					<div class="ball" v-for="ball in balls" v-show="ball.show" :key="ball.id">
						<div class="inner"></div>
					</div>
				</transition-group>
			</div>
			<transition name="fold">
				<div class="shopcart-list" v-show="listShow">
					<div class="list-header">
						<h1 class="title">购物车</h1>
						<span class="empty" @click="clearAll">清空</span>
					</div>
					<div class="list-content" ref="listcontent">
						<ul>
							<li v-for="food in selectFoods" class="food">
								<span class="name">{{food.name}}</span>
								<div class="price">
									<span>￥{{food.price*food.count}}</span>
								</div>
								<div class="cartcontrol-wrapper">
									<cartcontrol :food="food" v-on:cartadd="_drop($event)"></cartcontrol>
								</div>
							</li>
						</ul>
					</div>
				</div>
			</transition>
		</div>
		<transition name="fade">
		<div class="list-mask" v-show="listShow" @click="colseList"></div>
		</transition>
	</div>

</template>

<script type="text/ecmascript-6">
	import BScroll from 'better-scroll';
	import cartcontrol from 'components/cartcontrol/cartcontrol.vue';
	export default {
		props: {
			selectFoods: {
				type: Array,
				default() {
					return [];
				}
			},
			deliveryPrice: {
				type: Number,
				default: 0
			},
			minPrice: {
				type: Number,
				default: 0
			}
		},
		data() {
			return {
				balls: [{
					show: false,
					id: 1
				}, {
					show: false,
					id: 2
				}, {
					show: false,
					id: 3
				}, {
					show: false,
					id: 4
				}, {
					show: false,
					id: 5
				}],
				dropBalls: [],
				fold: true
			}
		},
		components: {
			cartcontrol
		},
		computed: {
			listShow() {
				if(!this.totalCount) {
					this.fold = true;
					return false;
				}
				let show = !this.fold;
				return show;
			},
			totalPrice() {
				let total = 0;
				this.selectFoods.forEach((food) => {
					total += food.price * food.count;
				});
				return total;
			},
			totalCount() {
				let count = 0;
				this.selectFoods.forEach((food) => {
					count += food.count;
				});
				return count;
			}
		},
		methods: {
			_drop(target) {
	 			this.$nextTick(() =>{
	 				this.drop(target);
	 			})
	 		},
			colseList() {
				this.fold = !this.fold;
			},
			clearAll() {
				this.selectFoods.forEach((food) => {
					food.count = 0;
				})
			},
			toggleList() {
				if(!this.totalCount) {
					this.fold = false;
					return;
				}
				this.fold = !this.fold;
				this.$nextTick(() => {
					if(!this.scroll) {
						this.scroll = new BScroll(this.$refs.listcontent, {
							click: true
						});
					} else {
						this.scroll.refresh();
					}

				})
			},
			drop(el) {
				for(let i = 0; i < this.balls.length; i++) {
					let ball = this.balls[i];
					if(!ball.show) {
						ball.show = true;
						ball.el = el;
						this.dropBalls.push(ball);
						return;
					}
				}
			},
			beforeEnter: function(el) {
				let count = this.balls.length;
				while(count--) {
					let ball = this.balls[count];
					if(ball.show) {
						let rect = ball.el.getBoundingClientRect();
						let x = rect.left - 32;
						let y = -(window.innerHeight - rect.top - 22);
						el.style.display = '';
						el.style.webkitTransform = `translate3d(0,${y}px,0)`;
						el.style.transform = `translate3d(0,${y}px,0)`;
						let inner = el.getElementsByClassName('inner')[0];
						inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
						inner.style.transform = `translate3d(${x}px,0,0)`;
					}
				}

			},
			enter: function(el) {
				let rt = el.offsetHeight;
				this.$nextTick(() => {
					el.style.webkitTransform = 'translate3d(0,0,0)';
					el.style.transform = 'translate3d(0,0,0)';
					let inner = el.getElementsByClassName('inner')[0];
					inner.style.webkitTransform = 'translate3d(0,0,0)';
					inner.style.transform = 'translate3d(0,0,0)';
				})
			},
			afterEnter: function(el) {
				let ball = this.dropBalls.shift();
				if(ball) {
					ball.show = false;
					el.style.display = 'none';
				}
			}
		}
	};
</script>

<style>
	.shopcart {
		position: fixed;
		bottom: 0;
		left: 0;
		width: 100%;
		height: 48px;
		z-index: 99;
	}
	
	.shopcart .content {
		position: relative;
		display: flex;
		background-color: #141d27;
		font-size: 0;
		color: rgba(255, 255, 255, .4);
		z-index: 99;
	}
	
	.shopcart .content-left {
		flex: 1;
	}
	
	.shopcart .content-left .logo-wrapper {
		display: inline-block;
		position: relative;
		top: -10px;
		margin: 0 12px;
		padding: 6px;
		width: 56px;
		height: 56px;
		box-sizing: border-box;
		vertical-align: top;
		border-radius: 50%;
		background-color: #141d27;
	}
	
	.shopcart .content-left .logo-wrapper .num {
		position: absolute;
		top: 0;
		right: 0;
		width: 24px;
		height: 16px;
		line-height: 16px;
		text-align: center;
		background-color: rgb(240, 20, 20);
		border-radius: 16px;
		font-size: 9px;
		font-weight: 700;
		color: #fff;
		box-shadow: 0 4px 8px 0 rgba(0, 0, 0, .4);
	}
	
	.shopcart .content-left .logo {
		width: 100%;
		height: 100%;
		border-radius: 50%;
		background: url(cart.png) no-repeat center #2b343c;
		background-size: 20px;
	}
	
	.shopcart .content-left .logo.highlight {
		background: url(cart-on.png) no-repeat center rgb(0, 160, 220);
		background-size: 20px;
	}
	
	.shopcart .content-left .price {
		display: inline-block;
		box-sizing: border-box;
		vertical-align: top;
		line-height: 24px;
		margin-top: 12px;
		padding-right: 12px;
		border-right: 1px solid rgba(255, 255, 255, .1);
		font-size: 16px;
		font-weight: 700;
		color: rgba(255, 255, 255, .4);
	}
	
	.shopcart .content-left .price.highlight {
		color: #fff;
	}
	
	.shopcart .content-left .desc {
		display: inline-block;
		vertical-align: top;
		line-height: 24px;
		margin: 12px 0 0 12px;
		font-size: 16px;
		font-size: 10px;
	}
	
	.shopcart .content-right {
		flex: 0 0 105px;
		width: 105px;
	}
	
	.shopcart .content-right .pay {
		height: 48px;
		line-height: 48px;
		font-size: 12px;
		text-align: center;
		font-weight: 700;
		background-color: #2b333b;
	}
	
	.shopcart .content-right .pay.pay-ok {
		background-color: #00b43c;
		color: #fff;
	}
	
	.ball-container {}
	
	.cart-yuan-enter-active,
	.cart-yuan-leave-active {
		transition: all 0.4s cubic-bezier(.49, -.29, .75, .41);
	}
	
	.cart-yuan-enter-active .inner,
	.cart-yuan-leave-active .inner {
		transition: all 0.4s linear;
	}
	
	.ball-container .ball {
		position: fixed;
		left: 32px;
		bottom: 22px;
		z-index: 200;
	}
	
	.ball-container .ball .inner {
		width: 16px;
		height: 16px;
		border-radius: 50%;
		background-color: rgb(0, 160, 220);
	}
	
	.shopcart-list {
		position: absolute;
		top: 0;
		left: 0;
		z-index: 88;
		width: 100%;
		transition: all .4s;
		transform: translate3d(0, -100%, 0);
	}
	
	.fold-leave-active {
		transform: translate3d(0, 0, 0);
	}
	
	.fold-enter,
	.fold-leave {
		transform: translate3d(0, 0, 0);
	}
	
	.shopcart-list .list-header {
		height: 40px;
		line-height: 40px;
		padding: 0 18px;
		background: #f3f5f7;
		border-bottom: 1px solid rgba(7, 17, 27, .1);
	}
	
	.shopcart-list .list-header .title {
		float: left;
		font-size: 14px;
		color: rgb(7, 17, 27);
	}
	
	.shopcart-list .list-header .empty {
		float: right;
		font-size: 12px;
		color: rgb(0, 160, 220);
	}
	
	.shopcart-list .list-content {
		padding: 0 18px;
		max-height: 217px;
		overflow: hidden;
		background: #fff;
	}
	
	.shopcart-list .list-content .food {
		position: relative;
		border-bottom: 1px solid rgba(7, 17, 27, .1);
		padding: 12px 0;
		box-sizing: border-box;
	}
	
	.shopcart-list .list-content .food .name {
		line-height: 24px;
		font-size: 14px;
		color: rgb(7, 17, 27);
	}
	
	.shopcart-list .list-content .food .price {
		position: absolute;
		right: 90px;
		bottom: 12px;
		line-height: 24px;
		font-size: 14px;
		font-weight: 700;
		color: rgb(240, 20, 20);
	}
	
	.shopcart .list-content .cartcontrol-wrapper {
		position: absolute;
		right: 0;
		bottom: 12px;
	}
	
	.list-mask {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background-color: rgba(7, 17, 27, .6);
		transition: all .4s;
		opacity: 1;
		z-index: 80;
	}
	.fade-enter,
	.fade-leave,
	.fade-leave-active {
		opacity: 0;
		background-color: rgba(7, 17, 27, 0);
	}
</style>