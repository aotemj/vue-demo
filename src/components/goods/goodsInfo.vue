<template>
	<div id="temp">
		<!-- 商品详情页 -->
		<div class="info">
			<!-- 商品详情轮播图 (mint-ui,子组件swipe)-->
			<swipe v-bind:imgs="goodsImgList"></swipe>

			<!-- 详情描述 -->
			<div class="desc">
					<h4 v-text="desc.title"></h4>
					<hr>
					<p>市场价:<span class="marketPrice">&yen;{{goodsInfo.market_price}}</span>销售价:<span class="salePrice">&yen;{{goodsInfo.sell_price}}</span></p>
					<p>购买数量: <inputNumber v-on:sendToParent="getCount"></inputNumber></p>
					<mt-button type="primary" size="small" >立即购买</mt-button>
					<mt-button type="danger" size="small" v-on:click="toShopCar">加入购物车</mt-button>
			</div>
			<div class="params">
				<h5>商品参数</h5>
				<hr>
				<p>商品货号:{{goodsInfo.goods_no}}</p>
				<p>库存情况:剩余 {{goodsInfo.stock_quantity}}</p>
				<p>上架时间:{{goodsInfo.add_time|dateformat("YYYY-MM-DD HH:mm:ss")}}</p>

			</div>
			<div class="comment">
				<router-link v-bind="{to:'/goods/goodsDesc/'+id}"><mt-button  type="primary" size="large">图文详情</mt-button></router-link>
				<router-link v-bind="{to:'/goods/goodsComment/'+id}"><mt-button type="danger" size="large">商品评论</mt-button></router-link>
			</div>

		</div>
	</div>
</template>
<script>
	/*引入接口公共前缀*/
	import common from "../../kits/common.js";

	/*引入swipe子组件(轮播)*/
	import swipe from "../subcom/swipe.vue";

	/*引入inputNumber子组件(获取购买数量)*/
	import inputNumber from "../subcom/inputNumber.vue";

	/*导入vm.js(实现非父子组件传值)*/
	import {vm} from "../../kits/vm.js";

	/*按需导入localSg.js,实现购物车信息增删操作(valueObj为新增对象的格式)*/
	import {setItem,valueObj} from "../../kits/localSg.js";

	export default{
		data(){
			return {
				id:0,
				desc:[],
				goodsImgList:[],
				goodsInfo:[],
				number:1
			}
		},
		created(){
			this.id = this.$route.params.id;
			this.getGoodsDesc();//获取商品描述
			this.getGoodsImgs();//获取商品图片
			this.getGoodsInfo();//获取商品参数和价格
		},
		methods:{
			getGoodsDesc(){//获取商品描述
				var url = common.apidomain+"/api/goods/getdesc/"+this.id;
				this.$http.get(url).then(function(response){
					this.desc=response.body.message[0];
				});
			},
			getGoodsImgs(){//获取商品图片
				var url = common.apidomain+"/api/getthumimages/"+this.id;
				this.$http.get(url).then(function(response){
					this.goodsImgList=response.body.message;
				});
			},
			//获取商品参数和价格
			getGoodsInfo(){
				var url = common.apidomain+"/api/goods/getinfo/"+this.id;
				this.$http.get(url).then(function(response){
					// console.log(response.body);
					this.goodsInfo=response.body.message[0];
				});
			},
			getCount(count){//获取当前购物车中商品的个数
				this.number=count;
				// this.$emit("sendData",this.number);
			},
			toShopCar(){//添加到购物车
				vm.$emit("sendNumber",this.number);//传值到app.vue
				valueObj.goodsId=this.id;
				valueObj.count=this.number;
				setItem(valueObj);
			}
		},
		components:{
			swipe,
			inputNumber
		}
	}
</script>
<style scoped>
	.mint-swipe, .mint-swipe-items-wrap{
		height:300px;
	}
	/* 轮播图图片样式 */
.mint-swipe, .mint-swipe-items-wrap img{
	width:100%;
	height:300px;
}
/*详情页*/
.desc,.params,.comment{
	padding:5px;
	border:1px solid gray;
	margin-bottom:5px;
	border-radius:5px;
}
.desc p span {
	margin-right:20px;
}
/*市场价*/
.marketPrice{
	text-decoration: line-through;
}
/*销售价*/
.salePrice{
	color:red;
}
h4{
	color:#26a2ff;
}
.mint-button--large{
	margin-bottom:5px;
}

</style>
