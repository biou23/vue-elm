<template>
  <div class="goods">
  	<div class="menu-wrapper" ref="menuwrapper">
  		<ul>
  			<li v-for="(item,index) in goods" class="menu-item" :class="{'active':currentIndex===index}" @click="selectMenu(index,$event)">
  				<span class="text border-1px">
  					<span class="icon" v-show="item.type>0" :class="classMap[item.type]"></span>
  					{{item.name}}
  				</span>
  			</li>
  		</ul>
  	</div>
  	<div class="foods-wrapper" ref="foodswrapper">
  		<ul>
  			<li v-for="item in goods" class="food-list j-food-list" >
  				<h1 class="title">{{item.name}}</h1>
  				<ul>
  					<li v-for="food in item.foods" class="food-item border-1px" @click="selectFood(food,$event)">
  						<div class="icon">
  							<img :src="food.icon"/>
  						</div>
  						<div class="content">
  							<h2 class="name">{{food.name}}</h2>
  							<p class="desc">{{food.description}}</p>
  							<div class="extra">
  								<span>月售{{food.sellCount}}份</span>
  								<span>好评率{{food.rating}}%</span>
  							</div>
  							<div class="price">
  								<span class="now">￥{{food.price}}</span><span v-show="food.oldPrice" class="old">{{food.oldPrice}}</span>
  							</div>
  							<div class="cartcontrol-wrapper">
  								<cartcontrol :food="food"  v-on:cartadd="_drop($event)"></cartcontrol>
  							</div>
  						</div>
  					</li>
  				</ul>
  			</li>
  		</ul>
  	</div>
 		<shopcart :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice" :select-foods="selectFoods"  ref="shopcart" ></shopcart>
 		<food :food="selectedFood" ref="selectedFood" v-on:cartadd="_drop($event)"></food>
  </div>
  
</template>

<script type="text/ecmascript-6">
	import BScroll from 'better-scroll';
	import shopcart from 'components/shopcart/shopcart.vue';
	import cartcontrol from 'components/cartcontrol/cartcontrol.vue';
	import food from 'components/food/food.vue';
	export default{
		props: {
			seller: {
				type: Object
			}
		},
		data() {
      return {
        goods: {},
        listHeight: [],
        scrollY: 0,
        selectedFood:{}
      };
    },
    components: {
      shopcart,
      cartcontrol,
      food
    },
    computed: {
    	currentIndex() {
    		for(let i=0; i<this.listHeight.length;i++){
    			let height1 = this.listHeight[i];
    			let height2 = this.listHeight[i+1];
    			if(!height2 || this.scrollY>=height1 && this.scrollY<height2){
    				return i;
    			}
    		}
    		return 0;
    	},
    	selectFoods() {
    		let foods = [];
    		if(this.goods.length){
    			this.goods.forEach( (good) => {
	    			good.foods.forEach((food) =>{
	    				if(food.count){
	    					foods.push(food)
	    				}
	    			})
    			})
    		}   		
    		return foods;
    	}
    },
		created() {
			this.classMap = ['decrease','discount','guanrantee','invoice','special'],
			this.$http.get('/api/goods').then((response) => {
				response = response.body;
				if (response.errno === 0) {
					this.goods = response.data;
					this.$nextTick(() => {
						
						this._initScroll();
						this._calculateHeight();
					})
					
				}
	    })
	 },
	 methods: { 
	 	selectFood(food,event){
	 		if(!event._constructed){
	 			return;
	 		}
	 		this.selectedFood = food;
	 		this.$refs.selectedFood.show();
	 	},
	 	selectMenu(index, event) {
	 		if(!event._constructed){
	 			return;
	 		}
	 		let foodList = this.$refs.foodswrapper.getElementsByClassName('j-food-list');
	 		this.foodsScroll.scrollToElement(foodList[index],300);
	 	},
	 	_initScroll() {
	 		this.meunScroll = new BScroll(this.$refs.menuwrapper, {
	 			click:true
	 		});
	 		
	 		this.foodsScroll = new BScroll(this.$refs.foodswrapper, {
	 			click:true
	 		});
	 		this.foodsScroll.on('scroll', (pos) =>{
	 			this.scrollY = Math.abs(Math.round(pos.y));
	 		})
	 	},
	 	_calculateHeight() {
	 		let foodList = this.$refs.foodswrapper.getElementsByClassName('j-food-list');
	 		let height = 0;
	 		this.listHeight.push(0);
	 		for(let i=0; i<foodList.length; i++){
	 			height += foodList[i].clientHeight;
	 			this.listHeight.push(height);		
	 		}
	 	},
	 	_drop(target) {
	 		this.$nextTick(() =>{
	 			this.$refs.shopcart.drop(target);
	 		})
	 	}
	 }
	};
</script>

<style>

.goods{
	display: flex;
	position: absolute;
	top: 174px;
	bottom: 46px;
	width: 100%;
	overflow: hidden;
}
.menu-wrapper{
	flex: 0 0 80px;
	width: 80px;
	background-color: #f3f5f7;
}
.menu-wrapper .menu-item{
	display: table;
	height: 54px;
	width: 56px;
	line-height: 14px;
	padding: 0 12px;
}
.menu-wrapper .menu-item.active{
	position: relative;
	margin-top: -1px;
	z-index: 10;
	background-color: #fff;
	font-weight: 700;	
}
.menu-wrapper .menu-item.active.text:after{
	display: none;
}
.menu-wrapper .menu-item .icon{
	display: inline-block;
	vertical-align: top;
	width: 12px;
	height: 12px;
	margin-right: 2px;
	background-size: 12px 12px;
	background-repeat: no-repeat;
}
.menu-wrapper .menu-item .text{
	font-size: 12px;
	display: table-cell;
	width: 56px;
	vertical-align: middle;
	position: relative;
}
.menu-wrapper .menu-item .text:after{
	position: absolute;
	left: 0;
	bottom: 0;
	width: 100%;
	border-top: 1px solid rgba(7,17,27,.1);
	content: ' ';
}
.menu-wrapper .menu-item .icon.decrease{
	background-image: url('decrease_3@2x.png');
}
.menu-wrapper .menu-item .icon.discount{
	background-image: url('discount_3@2x.png');
}
.menu-wrapper .menu-item .icon.guanrantee{
	background-image: url('guarantee_3@2x.png');
}
.menu-wrapper .menu-item .icon.invoice{
	background-image: url('invoice_3@2x.png');
}
.menu-wrapper .menu-item .icon.special{
	background-image: url('special_3@2x.png');
}

.foods-wrapper{
	flex: 1;
}
.foods-wrapper .title{
	padding-left: 14px;
	height: 26px;
	line-height: 26px;
	font-size: 12px;
	color: rgb(147,153,159);
	border-left: 2px solid #d9dde;
	background-color: #f3f5f7;
}
.foods-wrapper .food-item{
	display: flex;
	margin: 18px;
	padding-bottom: 18px;
	position: relative;
}
.foods-wrapper .food-item:after{
	position: absolute;
	left: 0;
	bottom: 0;
	width: 100%;
	border-top: 1px solid rgba(7,17,27,.1);
	content: ' ';
}
.foods-wrapper .food-item:last-child{
	margin-bottom: 0;
}
.foods-wrapper .food-item:last-child:after{
	display: none;
}
.foods-wrapper .food-item .icon{
	flex: 0 0 57px;
	width: 57px;
	height: 57px;
	margin-right: 10px;
}
.foods-wrapper .food-item .icon img{
	width: 57px;
	height: 57px;
}
.foods-wrapper .food-item .content{
	flex: 1;
	position: relative;
}
.foods-wrapper .food-item .content .cartcontrol-wrapper{
	position: absolute;
	right: 0;
	bottom: 0;
}
.foods-wrapper .food-item .content .name{
	margin: 2px 0px 8px 0;
	height: 14px;
	line-height: 14px;
	font-size: 14px;
	color: rgb(7,17,27);
}
.foods-wrapper .food-item .content .desc{
	margin-bottom: 8px;
	line-height: 12px;
	font-size: 10px;
	color: rgb(147,153,159);
}
.foods-wrapper .food-item .content .extra{
	line-height: 10px;
	font-size: 10px;
	color: rgb(147,153,159);
}
.foods-wrapper .food-item .content .extra:first-child{
	margin-right: 12px;
}
.foods-wrapper .food-item .content .price{
	font-weight: 700;
	line-height: 24px;
}
.foods-wrapper .food-item .content .price .now{
	margin-right: 8px;
	font-size: 14px;
	color: rgb(240,20,20);
}
.foods-wrapper .food-item .content .price .old{
	text-decoration: line-through;
	font-size: 10px;
	color: rgb(147,153,159);
}
</style>
