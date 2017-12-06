<template>
	<span>
		<!-- <div>数字变化组件</div> -->
		<span class="input">
			<span class="substrict" v-on:click="substrict">-</span>
			<span class="count" v-text="count"></span>
			<span class="add" v-on:click="add">+</span>
		</span>
	</span>
</template>
<script>
	export default {
		data(){
			return{
				count:1,
			}
		},
		created(){},
		methods:{
			substrict(){
				this.count--;
				if(this.count<1){
					this.count=1;
				}
				this.sendMessage();
			},
			add(){
				this.count++;
				this.sendMessage();
			},
			sendMessage(){//子组件向父组件传值
				this.$emit("sendToParent",this.count);
			}
		}
	}
</script>
<style scoped>
	span{
		display: inline-block;
	}
	.count{
		width:30px;
		text-align: center;
		border:1px solid #000;
		height: 20px;
	}
	.substrict,.add{
		text-align: center;
		width:30px;
		height:20px;
		border:1px solid #000;
	}
</style>
