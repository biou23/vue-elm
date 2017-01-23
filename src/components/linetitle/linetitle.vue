<template>
	<div class="v-title" >
		<div class="v-line"></div>
		<div class="v-text">{{myname}}</div>
		<div class="v-line"></div>
	</div>
</template>

<script type="text/ecmascript-6">
	export default {
		props: ['myname']
	};
</script>

<style>
.v-title{
	display: flex;
	width: 80%;
	margin: 28px auto 24px auto;
}
.v-title .v-line{
	flex: 1;
	position: relative;
	top: -6px;
	border-bottom: 1px solid rgba(255,255,255,.2);
}
.v-title .v-text{
	padding: 0 12px;
	font-weight: 700;
}
</style>