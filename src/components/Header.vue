<template>
  	<div class="header">
		<button @click="test">12121</button>
		<button @click="test1">22222</button>
		<button @click="test2">33333</button>
		<button @click="test3">44444</button>
		<button @click="test4">55555</button>
		<button @click="test5">66666</button>
		<button @click="test6">77777</button>
  	</div>
</template>

<script>
export default {
	methods:{
		test(){
			this.$router.push('/home/productSource')
		},
		test1(){
			this.$router.push('/home/productNeeds')
		},
		test2(){
			this.$router.push('/home/shoppingcart')
		},
		test3(){
			this.$router.push('/test1')
		},
		test4(){
			this.$router.push('/test2')
		},
		test5(){
			this.$router.push('/home/order')
		},
		test6(){
			this.$router.push('/test4')
		}
	}
}
</script>

<style scoped>
.header{
	position: relative;
	width:100%;
	height:70px;
	font-size:22px;
	margin-top:20px;
	border:1px solid red;
}
</style>