<template>
  <div id="app">
    <v-header :seller="seller"></v-header>
    <div class="tab border-1px">
    	<div class="tab-item">
    		 <router-link to="/goods">商品</router-link>
    	</div>
    	<div class="tab-item">
    		 <router-link to="/ratings">评论</router-link>
    	</div>
    	<div class="tab-item">
    		 <router-link to="/seller">商家</router-link>
    	</div>
    </div>
    <router-view :seller="seller"></router-view>
  </div>
</template>

<script type="text/ecmascript-6">
	import header from 'components/header/header.vue';
	export default {
		data() {
      return {
        seller: {}
      };
    },
    created() {
    	this.$http.get('/api/seller').then((response) => {
    		response = response.body;
    		if (response.errno === 0) {
    			this.seller = response.data;
    		}
    	})
    },
    components: {
      'v-header': header
    }
  };
</script>

<style>
.tab{
	display: flex;
	width: 100%;
	height: 40px;
	line-height: 40px;
	position: relative;
}
.tab:after{
	position: absolute;
	left: 0;
	bottom: 0;
	width: 100%;
	border-top: 1px solid rgba(7,17,27,.1);
	content: ' ';
}
.tab .tab-item{
	flex: 1;
	text-align: center;
}
.tab .tab-item a{
	display: block;
	font-size: 14px;
	color: rgb(77,85,93);
}
.tab a.router-link-active {
	color: red;
}
</style>
