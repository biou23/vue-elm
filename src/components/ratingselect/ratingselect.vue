 <template>
  <div class="ratingselect">
  	<div class="rating-type">
  		<span @click="select(2)" class="block positive" :class="{'active':selectType===2}">{{desc.all}}<span class="count">{{ratings.length}}</span></span>
  		<span @click="select(0)" class="block positive" :class="{'active':selectType===0}">{{desc.positive}}<span class="count">{{positives.length}}</span></span>
  		<span @click="select(1)" class="block negative" :class="{'active':selectType===1}">{{desc.negative}}<span class="count">{{negative.length}}</span></span>
  	</div>
  	<div @click="toggleContent()" class="switch" :class="{'on':onlyContent}">
  		<span class="icon"></span>
  		<span class="text">只看有内容的评价</span>
  	</div>
  </div>
</template>

<script type="text/ecmascript-6">
	const POSITIVE = 0;
	const NEGATIVE = 1;
	const ALL = 2;
	export default{
		props: {
			ratings: {
				type: Array,
				default() {
					return [];
				}
			},
			selectType:{
				type: Number,
				default:ALL
			},
			onlyContent: {
				type: Boolean,
				default:false
			},
			desc: {
				type:Object,
				default() {
					return {
						all: '全部',
						positive: '满意',
						negative: '不满意'
					};
				}
			}
		},
		computed:{
			positives(){
				return this.ratings.filter((rating)=>{
					return rating.rateType === POSITIVE;
				})
			},
			negative(){
				return this.ratings.filter((rating)=>{
					return rating.rateType === NEGATIVE;
				})
			}
		},
		methods: {
			select(type){
				this.selectType = type;
				this.$emit('ratingSelect',type);
			},
			toggleContent(){
				this.onlyContent = !this.onlyContent;
				this.$emit('ratingContent',this.onlyContent);
			}
		}
	};
</script>

<style>
.ratingselect .rating-type{
	padding: 18px 0;
	margin: 0 18px;
	border-bottom: 1px solid rgba(7,17,27,.1);
	font-size: 0;
}
.ratingselect .rating-type .block{
	display: inline-block;
	padding: 8px 12px ;
	margin-right: 8px;
	border-radius: 1px;
	color: rgb(77,85,93);
	font-size: 12px;
}
.ratingselect .rating-type .block.active{
	color: #fff;
}
.ratingselect .rating-type .block.positive{
	background-color: rgba(0,160,220,.2);
}
.ratingselect .rating-type .block.positive.active{
	background-color: rgb(0,160,220);
}
.ratingselect .rating-type .block.negative{
	background-color: rgba(77,85,93,.2);
}
.ratingselect .rating-type .block.negative.active{
	background-color: rgb(77,85,93);
}
.ratingselect .switch{
	padding: 12px 18px;
	line-height: 24px;
	border-bottom: 1px solid rgba(7,17,27,.1);
	color: rgb(147,159,159);
	font-size: 0;
}
.ratingselect .switch .icon{
	display: inline-block;
	margin-right: 4px;
	height: 24px;
	width: 24px;
	border-radius:50%;
	background-color: #999;
	vertical-align: top;
}
.ratingselect .switch.on .icon{
	background-color: #00B43C;
}
.ratingselect .switch .text{
	display: inline-block;
	vertical-align: top;
	font-size: 12px;
}
</style>
