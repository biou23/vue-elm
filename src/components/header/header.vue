<template>
  <div class="header">
  	<div class="content-wrapper">
  		<div class="avatar">
  			<img :src="seller.avatar" width="64" height="64"/>
  		</div>
  		<div class="content">
  			<div class="title">
  				<span class="brand"></span>
  				<span class="name">{{seller.name}}</span>
  			</div>
  			<div class="description">
  				{{seller.description}}/{{seller.deliveryTime}}分钟送达
  			</div>
  			<div class="support" v-if="seller.supports">
  				<span class="icon" :class="classMap[seller.supports[0].type]"></span>
  				<span class="text">{{seller.supports[0].description}}</span>
  			</div>
  		</div>
  		<div class="support-count" v-if="seller.supports" @click="showDetail">
  			<span class="count">{{seller.supports.length}}个</span>
  			<i class="right-btn"></i>
  		</div>
  	</div>
  	<div class="bulletin-wrapper" @click="showDetail">
  		<span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
  		<i class="right-btn"></i>
  	</div>
  	<div class="background">
  		<img :src="seller.avatar" width="100%" height="100%"/>
  	</div>
  	<transition name="fade">
	  	<div class="detail" v-show="detailShow" >
	  		<div class="detail-wrapper clearfix">
	  			<div class="detail-main">
	  				<h1 class="name">{{seller.name}}</h1>
	  				<div class="star-warp">
	  					<star :size="48" :score="seller.score"></star>
	  				</div>
	  				<linetitle :myname="lineName[0]"></linetitle>
	  				<ul v-if="seller.supports" class="supports">
	  					<li v-for="(item,index) in seller.supports" class="support-item">
	  						<span class="icon" :class="classMap[seller.supports[index].type]"></span>
	  						<span class="text">{{seller.supports[index].description}}</span>
	  					</li>
	  				</ul>
						<linetitle :myname="lineName[1]"></linetitle>
						<p class="detail-text">{{seller.bulletin}}</p>
	  			</div>
	  		</div>
	  		<div class="detail-close" @click="hiddenDetail">
	  			<i class="close-btn">+</i>
	  		</div>
	  	</div>
  	</transition>
  </div>
</template>

<script type="text/ecmascript-6">
	import star from 'components/star/star.vue';
	import linetitle from 'components/linetitle/linetitle.vue';
	export default{
		data() {
			return {
				detailShow: false
			}
		},
		props: {
			seller: {
				type: Object
			}
		},
		created() {
			this.classMap = ['decrease','discount','guanrantee','invoice','special'],
			this.lineName = ['优惠信息','商家公告']
		},
		methods: {
			showDetail() {
				this.detailShow = true;
			},
			hiddenDetail() {
				this.detailShow = false;
			}
		},
		components: {
      star,
      linetitle
    }
	};
</script>

<style>
.right-btn{
	display: inline-block;
	width: 5px;
	height: 7px;
	background: url(right-btn.png) no-repeat;
	background-size: 5px 7px;
}

body,html{
	line-height: 1;
	font-weight: 200;
	font-family: 'PingFang SC';
}
.header{
	color: #fff;
	position: relative;
	background-color: rgba(7,17,27,.5);
	overflow: hidden;
}
.content-wrapper{
	padding: 24px 12px 18px 24px;
	font-size: 0;
	position: relative;
}
.content-wrapper .avatar{
	display: inline-block;
	vertical-align: top;
}
.content-wrapper .avatar img{
	border-radius: 2px;
}
.content-wrapper .content{
	display: inline-block;
	margin-left: 16px;
	
}
.content-wrapper .content .title{
	margin: 2px 0 8px 0;
}
.content .title .brand{
	display: inline-block;
	width: 30px;
	height: 18px;
	background-image: url('brand@2x.png');
	background-size:30px 18px;
	vertical-align: top;
}
.content .title .name{
	margin-left: 6px;
	font-size: 16px;
	line-height: 18px;
	font-weight: bold;
}
.description{
	margin-bottom: 10px;
	line-height: 12px;
	font-size: 12px;
}
.support .icon{
	display: inline-block;
	vertical-align: top;
	width: 12px;
	height: 12px;
	margin-right: 4px;
	background-size: 12px 12px;
	background-repeat: no-repeat;
}
.support .icon.decrease{
	background-image: url('decrease_1@2x.png');
}
.support .icon.discount{
	background-image: url('discount_1@2x.png');
}
.support .icon.guanrantee{
	background-image: url('guarantee_1@2x.png');
}
.support .icon.invoice{
	background-image: url('invoice_1@2x.png');
}
.support .icon.special{
	background-image: url('special_1@2x.png');
}
.support .text{
	vertical-align: top;
	line-height: 12px;
	font-size: 10px;
}
.support-count{
	position: absolute;
	right: 12px;
	bottom: 14px;
	padding: 0 8px;
	height: 24px;
	line-height: 24px;
	border-radius: 14px;
	background-color: rgba(0,0,0,.2);
	text-align: center;
}
.support-count .count{
	font-size: 10px;
}
.support-count .right-btn{
	margin-left: 4px;
	margin-bottom: 1px;
}
.bulletin-wrapper{
	height: 28px;
	line-height: 28px;
	padding: 0 22px 0 12px;
	white-space: nowrap;
	overflow: hidden;
	text-overflow: ellipsis;
	position: relative;
	background-color: rgba(7,17,27,.2);
}
.bulletin-wrapper .right-btn{
	position: absolute;
	right: 12px;
	top: 12px;
}
.bulletin-title{
	display: inline-block;
	width: 22px;
	height: 12px;
	background-image: url('bulletin@2x.png');
	background-size: 22px 12px;
	margin-right: 4px;
	vertical-align: top;
	margin-top: 8px;
}
.bulletin-text{
	font-size: 10px;
	vertical-align: top;
}
.background{
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	z-index: -1;
	filter: blur(10px);
}
.header .detail{
	position: fixed;
	z-index: 100;
	top: 0;
	left: 0;
	height: 100%;
	width: 100%;
	overflow: auto;
	backdrop-filter:blur(10px);
	background-color: rgba(7,17,27,.8);
	
}
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s
}
.fade-enter, .fade-leave-active {
  opacity: 0
}
.detail-wrapper{
	min-height: 100%;
	width: 100%;
}
.detail-wrapper .detail-main{
	margin-top: 64px;
	padding-bottom: 64px;
}
.detail-main .name{
	line-height: 16px;
	text-align: center;
	font-size: 16px;
	font-weight: 700;
}
.detail-main .star-warp{
	margin-top: 18px;
	padding: 2px 0;
	text-align: center;
}

.detail-main .supports{
	width: 80%;
	margin: 0 auto;
}
.detail-main .supports .support-item{
	padding: 0 12px;
	margin-bottom: 12px;
	font-size: 0;
}
.detail-main .supports .support-item:last-child{
	margin-bottom: 0;
}
.detail-main .supports .support-item .icon{
	display: inline-block;
	vertical-align: top;
	width: 16px;
	height: 16px;
	margin-right: 6px;
	background-size: 16px 16px;
	background-repeat: no-repeat;
}
.detail-main .supports .icon.decrease{
	background-image: url('decrease_2@2x.png');
}
.detail-main .supports .icon.discount{
	background-image: url('discount_2@2x.png');
}
.detail-main .supports .icon.guanrantee{
	background-image: url('guarantee_2@2x.png');
}
.detail-main .supports .icon.invoice{
	background-image: url('invoice_2@2x.png');
}
.detail-main .supports .icon.special{
	background-image: url('special_2@2x.png');
}
.detail-main .supports .support-item .text{
	line-height: 16px;
	font-size: 12px;
}
.detail-close{
	position: relative;
	margin: -64px auto 0 auto;
	height: 32px;
	width: 32px;
	clear: both;
	text-align: center;
}
.detail-close .close-btn{
	display: block;
	font-size: 32px;
	font-style: normal;
	transform:rotate(45deg);
}
.detail-text{
	padding: 0 48px;
	line-height: 23px;
	font-size: 12px;
}
</style>
