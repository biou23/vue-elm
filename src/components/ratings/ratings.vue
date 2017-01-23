<template>
	<div class="ratings" ref="ratings">
		<div class="ratings-content">
			<div class="overview">
				<div class="overview-left">
					<h1 class="score">{{seller.score}}</h1>
					<div class="title">综合评分</div>
					<div class="rank">
						高于周边商家{{seller.rankRate}}%
					</div>
				</div>
				<div class="overview-right">
					<div class="score-warpper">
						<span class="title">服务态度</span>
						<star :size="36" :score="seller.serviceScore"></star>
						<span class="score">{{seller.serviceScore}}</span>
					</div>
					<div class="score-warpper">
						<span class="title">商品评分</span>
						<star :size="36" :score="seller.foodScore"></star>
						<span class="score">{{seller.foodScore}}</span>
					</div>
					<div class="deliveryTime-warpper">
						<span class="title">送达时间</span>
						<span class="delivery">{{seller.deliveryTime}}分钟</span>
					</div>
				</div>
			</div>
			<split></split>
		<ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="ratings" v-on:ratingSelect="wSelect($event)" v-on:ratingContent="wContent($event)"></ratingselect>
		<div class="rating-wrapper">
        <ul>
          <li v-for="rating in ratings" v-show="needShow(rating.rateType, rating.text)" class="rating-item">
            <div class="avatar">
              <img width="28" height="28" :src="rating.avatar">
            </div>
            <div class="content">
              <h1 class="name">{{rating.username}}</h1>
              <div class="star-wrapper">
                <star :size="24" :score="rating.score"></star>
                <span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}</span>
              </div>
              <p class="text">{{rating.text}}</p>
              <div class="recommend" v-show="rating.recommend && rating.recommend.length">
                <span class="item" v-for="item in rating.recommend">{{item}}</span>
              </div>
              <div class="time">
                {{rating.rateTime | formatDate}}
              </div>
            </div>
          </li>
        </ul>
      </div>
		</div>
	</div>
</template>

<script type="text/ecmascript-6">
	import BScroll from 'better-scroll';
	import star from 'components/star/star.vue';
	import split from 'components/split/split.vue';
	import ratingselect from 'components/ratingselect/ratingselect.vue';

	const POSITIVE = 0;
	const NEGATIVE = 1;
	const ALL = 2;
	export default {
		data() {
				return {
					ratings: [],
					selectType: ALL,
					onlyContent: false,
					desc: {
						all: '全部',
						positive: '满意',
						negative: '不满意'
					}
				}
			},
			props: {
				seller: {
					type: Object
				}
			},
			components: {
				star,
				split,
				ratingselect
			},
			created() {
				this.$http.get('/api/ratings').then((response) => {
					response = response.body
					if(response.errno === 0) {
						this.ratings = response.data;
						this.$nextTick(() => {
            	this.scroll = new BScroll(this.$refs.ratings, {
              click: true
            });
          });
					}
				})
			},
			methods: {
				wContent(type) {
					this.onlyContent = type;
				},
				wSelect(type) {
					this.selectType = type;
				},
				needShow(type, text) {
	        if (this.onlyContent && !text) {
	          return false;
	        }
	        if (this.selectType === ALL) {
	          return true;
	        } else {
	          return type === this.selectType;
	        }
      	}
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
		}
	};
</script>

<style>
	.ratings {
		position: absolute;
		top: 174px;
		left: 0;
		bottom: 0;
		width: 100%;
		overflow: hidden;
	}
	
	.ratings .overview {
		display: flex;
		padding: 18px 0;
	}
	
	.ratings .overview-left {
		flex: 0 0 137px;
		width: 137px;
		border-right: 1px solid rgba(7, 17, 27, .1);
		text-align: center;
	}
	
	@media only screen and (max-width: 320px) {
		.ratings .overview-left {
			flex: 0 0 110px;
			width: 110px;
		}
		.ratings .overview .overview-right {
			padding-left: 6px;
		}
	}
	
	.ratings .overview-left .score {
		margin-bottom: 6px;
		line-height: 28px;
		font-size: 24px;
		color: rgb(255, 153, 0);
	}
	
	.ratings .overview-left .title {
		margin-bottom: 8px;
		line-height: 12px;
		font-size: 12px;
		color: rgb(7, 17, 27);
	}
	
	.ratings .overview-left .rank {
		padding-bottom: 6px;
		line-height: 10px;
		font-size: 10px;
		color: rgb(147, 153, 159);
	}
	
	.ratings .overview-right {
		flex: 1;
		padding: 6px 0 6px 24px;
	}
	
	.ratings .overview-right .score-warpper {
		margin-bottom: 8px;
		line-height: 18px;
		font-size: 0;
	}
	
	.ratings .overview-right .score-warpper .title {
		font-size: 12px;
		color: rgb(7, 17, 27);
	}
	
	.ratings .overview-right .score-warpper .star {
		display: inline-block;
		margin: 0 12px;
		vertical-align: top;
	}
	
	.ratings .overview-right .score-warpper .score {
		display: inline-block;
		vertical-align: top;
		font-size: 12px;
		color: rgb(255, 153, 0);
	}
	
	.ratings .overview-right .deliveryTime-warpper {
		font-size: 0;
	}
	
	.ratings .overview-right .deliveryTime-warpper .title {
		line-height: 18px;
		vertical-align: top;
		font-size: 12px;
		color: rgb(7, 17, 27);
	}
	
	.overview-right .deliveryTime-warpper .delivery {
		font-size: 12px;
		color: rgb(147, 153, 159);
		margin-left: 12px;
		line-height: 18px;
	}
	
	.ratings .rating-wrapper {
		padding: 0 18px;
	}
	
	.ratings .rating-wrapper .rating-item {
		display: flex;
		padding: 18px 0;
		border-bottom: rgba(7, 17, 27, 0.1);
	}
	
	.ratings .rating-wrapper .rating-item .avatar {
		flex: 0 0 28px;
		width: 28px;
		margin-right: 12px;
	}
	
	.ratings .rating-wrapper .rating-item .avatar img {
		border-radius: 50%;
	}
	
	.ratings .rating-wrapper .rating-item .content {
		position: relative;
		flex: 1;
	}
	
	.ratings .rating-wrapper .rating-item .content .name {
		margin-bottom: 4px;
		line-height: 12px;
		font-size: 10px;
		color: rgb(7, 17, 27);
	}
	
	.ratings .rating-wrapper .rating-item .content .star-wrapper {
		margin-bottom: 6px;
		font-size: 0;
	}
	
	.ratings .rating-wrapper .rating-item .content .star-wrapper .star {
		display: inline-block;
		margin-right: 6px;
		vertical-align: top;
	}
	
	.ratings .rating-wrapper .rating-item .content .star-wrapper .delivery {
		display: inline-block;
		vertical-align: top;
		line-height: 12px;
		font-size: 10px;
		color: rgb(147, 153, 159);
	}
	
	.ratings .rating-wrapper .rating-item .content .text {
		margin-bottom: 8px;
		line-height: 18px;
		color: rgb(7, 17, 27);
		font-size: 12px;
	}
	
	.ratings .rating-wrapper .rating-item .content .recommend {
		line-height: 16px;
		font-size: 0;
	}
	
	.ratings .rating-wrapper .rating-item .content .recommend .item {
		display: inline-block;
		margin: 0 8px 4px 0;
		font-size: 9px;
		padding: 0 6px;
		border: 1px solid rgba(7, 17, 27, 0.1);
		border-radius: 1px;
		color: rgb(147, 153, 159);
		background: #fff;
	}
	
	.ratings .rating-wrapper .rating-item .content .time {
		position: absolute;
		top: 0;
		right: 0;
		line-height: 12px;
		font-size: 10px;
		color: rgb(147, 153, 159);
	}
</style>