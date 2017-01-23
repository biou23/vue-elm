<template>
	<transition name="move">
		<div class="food" v-show="showFlag" ref="food">
			<div class="food-content">
				<div class="image-header">
					<img :src="food.image" />
					<span class="back-btn" @click="hide"></span>
				</div>
				<div class="content">
					<h1 class="title">{{food.name}}</h1>
					<div class="detail">
						<span class="sell-count">月售{{food.sellCount}}</span>
						<span class="rating">好评率{{food.rating}}</span>
					</div>
					<div class="price">
						<span class="now">￥{{food.price}}</span><span v-show="food.oldPrice" class="old">{{food.oldPrice}}</span>
					</div>
					<div class="cartcontrol-wrapper">
						<cartcontrol :food="food" v-on:cartadd="cartAdd"></cartcontrol>
					</div>
					<transition name="firstfade">
						<div class="buy" v-show="!food.count || food.count===0" @click="firstAdd">
							加入购物车
						</div>
					</transition>
				</div>
				<split></split>
				<div class="info">
					<h1 class="title">商品信息</h1>
					<p class="text">{{food.info}}</p>
				</div>
				<split></split>
				<div class="rating">
					<h1 class="title">商品评价</h1>
					<ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc" 
					:ratings="food.ratings" v-on:ratingSelect="wSelect($event)" v-on:ratingContent="wContent($event)"></ratingselect>
					<div class="rating-wrapper">
						<ul v-show="food.ratings && food.ratings.length" >
							<li v-for="rating in food.ratings" class="rating-item" v-show="needShow(rating.rateType,rating.text)">
								<div class="user">
									<span class="name">{{rating.username}}</span>
									<img :src="rating.avatar" class="avatar" width="12" height="12"/>
								</div>
								<div class="time">
									{{rating.rateTime | formatDate}}
								</div>
								<p class="text">
									<span :class="{'icon-0':rating.rateType===0,'icon-1':rating.rateType===1}"></span>
									{{rating.text}}
								</p>
							</li>
						</ul>
						<div class="no-rating" v-show="!food.ratings || !food.ratings.length">
							暂无评价
						</div>
					</div>
				</div>
			</div>
		</div>
	</transition>
</template>

<script type="text/ecmascript-6">
	import Vue from 'vue';
	import BScroll from 'better-scroll';
	import cartcontrol from 'components/cartcontrol/cartcontrol.vue';
	import split from 'components/split/split.vue';
	import ratingselect from 'components/ratingselect/ratingselect.vue';
	
	const POSITIVE = 0;
	const NEGATIVE = 1;
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
				onlyContent: false,
				desc: {
					all: '全部',
					positive: '推荐',
					negative: '吐槽'
				}
			}
		},
		components: {
			cartcontrol,
			split,
			ratingselect
		},
		filters:{
			formatDate(time) {
				let data = new Date(time);
				let year = data.getFullYear();
                let month =(data.getMonth() + 1).toString();
                let day = (data.getDate()).toString();
                if (month.length == 1) {
                    month = "0" + month;
                }
                if (day.length == 1) {
                    day = "0" + day;
                }
                let hour = data.getHours();
                let minute = data.getMinutes();
                let dateTime = year+'-'+month+'-'+day+' '+hour+':'+minute;
				return dateTime;  
			}
		},
		methods: {
			needShow(type,text){
				if(this.onlyContent && !text){
					return false;
				}
				if(this.selectType===ALL){
					return true;
				}
				if(this.selectType!=ALL){
					return this.selectType===type;
				}
			},
			wContent(type){
				this.onlyContent = type;
			},
			wSelect(type){
				this.selectType = type;
			},
			cartAdd() {
				this.$emit('cartadd', event.target);
			},
			show() {
				this.showFlag = true;
				this.$nextTick(() => {
					if(!this.scroll) {
						this.scroll = new BScroll(this.$refs.food, {
							click: true
						});
					} else {
						this.scroll.refresh();
					}
				})
			},
			hide() {
				this.showFlag = false;
			},
			firstAdd(event) {
				if(!event._constructed) {
					return;
				}
				Vue.set(this.food, 'count', 1);
				this.$emit('cartadd', event.target);
			}
		}
	};
</script>

<style>
	.food {
		position: fixed;
		left: 0;
		top: 0;
		bottom: 48px;
		z-index: 30;
		width: 100%;
		background-color: #fff;
		transition: all .2s linear;
		transform: translate3d(0, 0, 0);
	}
	
	.move-enter-active,
	.move-leave-active {
		transform: translate3d(100%, 0, 0);
	}
	
	.move-enter,
	.move-leave {
		transform: translate3d(100%, 0, 0);
	}
	
	.food .image-header {
		position: relative;
		width: 100%;
		height: 0;
		padding-top: 100%;
	}
	
	.food .image-header img {
		position: absolute;
		left: 0;
		top: 0;
		height: 100%;
		width: 100%;
	}
	
	.food .image-header .back-btn {
		position: absolute;
		top: 10px;
		left: 10px;
		height: 18px;
		width: 30px;
		background: url(back-btn.png) no-repeat center;
	}
	
	.food .content {
		padding: 18px;
		position: relative;
		padding-bottom: 48px;
	}
	
	.food .content .title {
		line-height: 14px;
		margin-bottom: 8px;
		font-size: 14px;
		font-weight: 700;
		color: 7, 17, 27;
	}
	
	.food .content .detail {
		margin-bottom: 18px;
		line-height: 10px;
		font-size: 0;
	}
	
	.food .content .detail .sell-count,
	.food .content .detail .rating {
		font-size: 10px;
		color: rgb(147, 153, 159);
	}
	
	.food .cartcontrol-wrapper {
		position: absolute;
		right: 18px;
		bottom: 18px;
	}
	
	.food .buy {
		position: absolute;
		right: 18px;
		bottom: 18px;
		z-index: 10;
		height: 24px;
		line-height: 24px;
		padding: 0 12px;
		box-sizing: border-box;
		border-radius: 12px;
		color: #fff;
		font-size: 10px;
		background: rgb(0, 160, 220);
		transition: all .2s;
		opacity: 1;
	}
	
	.food .firstfade-enter-active,
	.food .firstfade-leave-active,
	.food .firstfade-enter,
	.food .firstfade-leave {
		opacity: 0;
	}
	.food .info{
		padding: 18px;
	}
	.food .info .title{
		line-height: 14px;
		margin-bottom: 6px;
		font-size: 14px;
		color: rgb(7,17,27);
	}
	.food .info .text{
		line-height: 24px;
		padding: 0 8px;
		font-size: 12px;
		color: rgb(77,85,93);
	}
	.food .rating{
		padding-top: 18px;
	}
	.food .rating .title{
		line-height: 14px;
		margin-left: 10px;
		font-size: 14px;
		color: rgb(7,17,27);
	}
	.food .rating .rating-wrapper{
		padding: 0 18px 0px 18px;
	}
	.food .rating .rating-item{
		position: relative;
		padding: 16px 0;
		border-bottom: 1px solid rgba(7,17,27,.1);
	}
	.food .rating .rating-item .user{
		position: absolute;
		right: 0;
		top: 16px;
		line-height: 12px;
		font-size: 0;
	}
	.food .rating .rating-item .user .name{
		display: inline-block;
		vertical-align: top;
		margin-right: 6px;
		font-size: 10px;
		color: rgb(147,153,159);
	}
	
	.food .rating .rating-item .user .avatar{
		border-radius:50% ;
	}
	.food .rating .rating-item .time{
		font-size: 10px;
		line-height: 12px;
		margin-bottom: 6px;
		color: rgb(147,153,159);
	}
	.food .rating .rating-item .text{
		line-height: 16px;
		font-size: 12px;
	}
	.food .rating .rating-item .text span{
		display: inline-block;
		height: 12px;
		width: 10px;
		position: relative;
		top: 2px;
		margin-right: 5px;
	}
	.food .rating .rating-item .text .icon-0{
		background: url(icon-0.png) no-repeat center;
		background-size: 12px 10px;
	}
	.food .rating .rating-item .text .icon-1{
		background: url(icon-1.png) no-repeat center;
		background-size: 12px 10px;
	}
	.food .rating .no-rating{
		padding: 16px 0;
		font-size: 12px;
		color: rgb(147,153,159);
	}
</style>