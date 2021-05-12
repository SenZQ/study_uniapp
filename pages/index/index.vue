<template>
	<view class="content">
		
		<!-- 轮播图 -->
		<swiper class="banner" indicator-dots autoplay loop>
			<swiper-item>
				<image src="../../static/banner1.png" mode="widthFix"></image>
			</swiper-item>
			<swiper-item>
				<image src="../../static/banner2.png" mode="widthFix"></image>
			</swiper-item>
		</swiper>
		
		<!-- tab 栏 -->
		<view class="btn_list">
			<view :class='{"btn_hover":true,"hover0":(tabNum==0)&&true,"hover1":(tabNum==1)&&true,"hover2":(tabNum==2)&&true}'></view>
			<view class="btn" @click="ChangeTab(0)">外卖</view>
			<view class="btn" @click="ChangeTab(1)">评论</view>
			<view class="btn" @click="ChangeTab(2)">详情</view>
		</view>
		
		<!-- 内容视窗 -->
		<swiper class="page_content" :current="tabNum" disable-touch>
			<swiper-item class="page_content_item">
				<view class="info_bar"><uni-icons type="info-filled" size="12"></uni-icons><text style="padding-left: 10rpx;">重大利好</text></view>
				<view class="menu">
					
					<!-- 左侧板块选择 -->
					<scroll-view class="tab_list" scroll-y>
						
						<view v-for="( item , index ) in menu" :class='{"tab_item":true,"hover":(menuNum==index)&&true}' @click="ChangeMenu(index)">{{ item.class+" "+(index+1) }}</view>
						<view class="bottom"></view>
						
					</scroll-view>
					
					<!-- 右侧菜单目录 -->
					<scroll-view class="good_list" scroll-y>
						
						<view class="good_item" v-for="( item , index ) in menu[menuNum].list">
							<image class="img" mode="aspectFit" src="../../static/logo.png"></image>
							<view class="name">{{ item.name+" "+(menuNum+1)+"-"+(index+1) }}</view>
							<view class="price">￥{{ (index+1) }}</view>
							<view class="btn" @click='setGood( item.name+" "+(menuNum+1)+"-"+(index+1) , (index+1) )'>选规格</view>
						</view>
						<view class="bottom"></view>
						
					</scroll-view>
				</view>
			</swiper-item>
			<swiper-item>222222222
			</swiper-item>
			<swiper-item>33333333333
			</swiper-item>
		</swiper>
		
		<!-- 遮盖背景 -->
		<view class="bg" :style='{"display":(cartData.open||optData.open)?"block":"none"}' @click="closeAll"></view>
		
		<!-- 购物车 -->
		<view class="cart_bar">
			<view class="price" @click="openCart">￥{{ totalPrice }}</view>
			<view class="pay">去结算</view>
		</view>
		
		<!-- 购物车内容 -->
		<view class="cart_content" :style='{"display":cartData.open?"block":"none"}'>
			<view class="info">
				<view class="title">
					<text>购物车</text>
				</view>
				<view class="reset" @click="resetCart">
					<uni-icons type="trash" size="12"></uni-icons><text>清空</text>
				</view>
			</view>
			
			<scroll-view class="cart_scroll" scroll-y="">
				
				<!-- 购物车里面的商品 -->
				<view class="cart_item" v-for='( item , index ) in cartData.goods' :key='item.id'>
					<view class="title">
						<text class="name">{{ item.name }}</text><br/>
						<text class="tag">
							{{ (item.cold==1)?"正常冰":"" }}{{ (item.cold==2)?"少冰":"" }}{{ (item.cold==3)?"去冰":"" }}{{ (item.cold==4)?"常温":"" }} {{ (item.sweet==1)?"标准":"" }}{{ (item.sweet==2)?"七分糖":"" }}{{ (item.sweet==3)?"五分糖":"" }}{{ (item.sweet==4)?"三分糖":"" }}
						</text>
					</view>
					<view class="price">￥{{ item.price }}</view>
					<view class="del" @click='delGood(item.id)'><uni-icons type="trash" size="25"></uni-icons></view>
				</view>
				
				<view class="bottom"></view>
				
			</scroll-view>
		</view>
		
		<!-- 奶茶配置表单 -->
		<view class="opt_content" :style='{"display":optData.open?"block":"none"}'>
			
			<view class="close" @click="closeAll">
				<uni-icons type="minus" size="20"></uni-icons>
			</view>
			
			<view class="title">{{ optData.name }}</view>
			
			<view class="title_s">甜度：</view>
			<view class="group">
				<view class="radio" :class='{"on":(optData.sweet==1)}' @click='setDetail("sweet",1)'>标准</view>
				<view class="radio" :class='{"on":(optData.sweet==2)}' @click='setDetail("sweet",2)'>七分糖</view>
				<view class="radio" :class='{"on":(optData.sweet==3)}' @click='setDetail("sweet",3)'>五分糖</view>
				<view class="radio" :class='{"on":(optData.sweet==4)}' @click='setDetail("sweet",4)'>三分糖</view>
			</view>
			
			<view class="title_s">温度：</view>
			<view class="group">
				<view class="radio" :class='{"on":(optData.cold==1)}' @click='setDetail("cold",1)'>正常冰</view>
				<view class="radio" :class='{"on":(optData.cold==2)}' @click='setDetail("cold",2)'>少冰</view>
				<view class="radio" :class='{"on":(optData.cold==3)}' @click='setDetail("cold",3)'>去冰</view>
				<view class="radio" :class='{"on":(optData.cold==4)}' @click='setDetail("cold",4)'>常温</view>
			</view>
			
			<view class="pay_bar">
				<view class="price">￥{{ optData.price }}</view>
				<view class="btn" @click="addToCart">加入购物车</view>
			</view>
			
		</view>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				tabNum: 0,
				menuNum: 0,
				cartData:{
					open: false,
					goods:[
						
					],
				},
				optData:{
					open: false,
					sweet: 1,
					cold: 1,
					name: "",
					price: "",
				},
				cart:[
					
				],
				menu:[
					{
						class:"板块",
						list:[
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							}
						]
					},
					{
						class:"板块",
						list:[
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							}
						]
					},
					{
						class:"板块",
						list:[
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							}
						]
					},
					{
						class:"板块",
						list:[
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							}
						]
					},
					{
						class:"板块",
						list:[
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							}
						]
					},
					{
						class:"板块",
						list:[
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							}
						]
					},
					{
						class:"板块",
						list:[
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							},
							{
								"name":"奶茶",
								"price":1
							}
						]
					},
				]
			}
		},
		computed:{
			totalPrice(){
				var price = 0;
				this.cartData.goods.map(item=>{
					price += item.price
				});
				return price;
			}
		},
		onLoad() {

		},
		methods: {
			// 改变内容
			ChangeTab( num ){
				this.tabNum = num;
			},
			// 改变板块
			ChangeMenu( num ){
				this.menuNum = num;
			},
			// 弹出设置商品菜单
			setGood( name , price ){
				this.optData.open = true;
				this.optData.name = name;
				this.optData.price = price;
			},
			// 关闭所有功能
			closeAll(){
				this.optData.open = false;
				this.cartData.open = false;
			},
			// 定制商品
			setDetail( name , num ){
				this.optData[name] = num;
			},
			// 将商品添加到购物车
			addToCart(){
				var id = new Date().getTime();
				var good = {
					name:  this.optData.name,
					sweet: this.optData.sweet,
					cold:  this.optData.cold,
					price: this.optData.price,
					id:    id
				}
				this.cartData.goods.push(good);
				this.closeAll();
			},
			// 打开购物车
			openCart(){
				this.cartData.open = true;
			},
			// 清空购物车
			resetCart(){
				uni.showModal({
				    title: "确定清空购物车？",
				    icon: "none",
					success:(stat)=>{
						if( stat.confirm!=undefined && stat.confirm==true ){
							console.log("清空");
							this.cartData.open = false;
							this.cartData.goods = [];
						}
					}
				})
			},
			// 删除商品
			delGood( id ){
				for( var i = 0 ; i < this.cartData.goods.length ; i++ ){
					if( this.cartData.goods[i].id == id ){
						this.cartData.goods.splice(i,1);
						break;
					}
				}
			}
		}
	}
</script>

<style lang="less" scoped>
	.content {
		position: fixed;
		left: 0;
		top: 0;
		width: 100%;
		height: 100%;
		box-sizing: border-box;
		padding-top: 490rpx;
		
		.banner{
			position: absolute;
			left: 0;
			top: 0;
			width: 100%;
			height: 400rpx;
		}
		
		.btn_list{
			position: absolute;
			left: 0;
			top: 400rpx;
			height: 90rpx;
			width: 100%;
			display: flex;
			
			.btn{
				display: flex;
				height: 100%;
				flex: 1;
				align-items: center;
				justify-content: center;
				font-size: 14rpx;
			}
			
			.btn_hover{
				position: absolute;
				top: 0;
				width: 250rpx;
				height: 100%;
				box-sizing: border-box;
				border-bottom: solid 7rpx #007AFF;
				transition: all 0.3s;
				
				&.hover0{
					left: 0rpx;
				}
				&.hover1{
					left: 250rpx;
				}
				&.hover2{
					left: 500rpx;
				}
			}
		}
		
		.page_content{
			position: relative;
			width: 100%;
			height: 100%;
			
			.page_content_item{
				position: relative;
				width: 100%;
				height: 100%;
				box-sizing: border-box;
				padding-top: 50rpx;
				padding-bottom: 119rpx;
				z-index: -1;
				
				.info_bar{
					position: absolute;
					top: 0;
					width: 100%;
					padding-left: 15rpx;
					height: 50rpx;
					line-height: 50rpx;
					font-size: 12rpx;
					background-color: #FFF;
					z-index: 1;
					border-bottom: solid 1rpx #eee;
				}
				
				.menu{
					position: relative;
					width: 100%;
					height: 100%;
					display: flex;
					
					.bottom{
						width: 100%;
						height: 100rpx;
					}
					
					.tab_list{
						display: flex;
						flex: 1.2;
						
						.tab_item{
							position: relative;
							width: 95%;
							height: 120rpx;
							border-left: solid thick #CCC;
							border-bottom: solid thin #999;
							background-color: #CCC;
							color: #555;
							font-size: small;
							box-sizing: border-box;
							display: flex;
							align-items: center;
							justify-content: center;
							
							&.hover{
								background-color: #FFF;
								color: #007AFF;
								border: none;
								border-left: solid thick #007AFF;
							}
						}
					}
					
					.good_list{
						display: flex;
						flex: 3;
						
						.good_item{
							position: relative;
							width: 99%;
							height: 160rpx;
							border-bottom: solid thin #999;
							box-sizing: border-box;
							padding: 30rpx 10rpx 30rpx 10rpx;
							
							.img{
								width: 110rpx;
								height: 110rpx;
							}
							
							.name{
								position: absolute;
								left: 140rpx;
								top: 20rpx;
								font-size: medium;
								font-weight: bold;
								color: #2C405A;
							}
							.price{
								position: absolute;
								left: 135rpx;
								bottom: 15rpx;
								font-size: small;
								color: #b50230;
							}
							.btn{
								position: absolute;
								right: 10rpx;
								bottom: 15rpx;
								width: 150rpx;
								height: 60rpx;
								border-radius: 30rpx;
								display: flex;
								align-items: center;
								justify-content: center;
								background-color: #017aff;
								color: #FFF;
								font-size: small;
							}
						}
					}
				}
			}
		}
		
		.bg{
			position: fixed;
			left: 0;
			bottom: 119rpx;
			width: 100%;
			height: 100%;
			background-color: #000;
			opacity: 0.7;
		}
		
		.cart_bar{
			position: fixed;
			left: 0;
			width: 100%;
			height: 100rpx;
			bottom: 119rpx;
			border-top: solid thin #999999;
			background-color: #FFF;
			display: flex;
			
			.price{
				display: flex;
				height:100%;
				flex: 2.5;
				align-items: center;
				font-size: large;
				font-weight: bold;
				color: #b50230;
				padding-left: 50rpx;
			}
			.pay{
				display: flex;
				height:100%;
				flex: 1.5;
				background-color: #007AFF;
				align-items: center;
				justify-content: center;
				color: #FFF;
				font-size: small;
			}
		}
		
		.cart_content{
			position: fixed;
			left: 0;
			width: 100%;
			height: auto;
			bottom: 219rpx;
			background-color: #FFF;
			max-height: 850rpx;
			padding-top: 75rpx;
			
			.cart_scroll{
				position: relative;
				max-height: 850-75rpx;
			}
			
			.info{
				position: absolute;
				top: 0%;
				left: 0;
				height: 75rpx;
				width: 100%;
				background-color: #eee;
				display: flex;
				font-size: small;
				color: #555;
				
				.title{
					flex: 4;
					display:flex;
					align-items: center;
					
					text{
						margin-left: 30rpx;
						padding-left: 20rpx;
						border-left: solid 30rpx #007AFF;
					}
				}
				.reset{
					flex: 1;
					display:flex;
					align-items: center;
					
					text{
						margin-left: 10rpx;
					}
				}
			}
		
			.cart_item{
				position: relative;
				width: 100%;
				height: 110rpx;
				border-bottom: dashed thin #999;
				display: flex;
				
				view{
					display: flex;
					align-items: center;
				}
				
				.title{
					flex: 3;
					color: #2C405A;
					padding: 15rpx 0 0 50rpx;
					display: block;
					line-height: 40rpx;
					
					.tag{
						font-size: xx-small;
						color: #808080;
					}
				}
				.price{
					flex: 1;
					color: #b50230;
					font-weight: bold;
					justify-content: center;
				}
				.del{
					flex: 1;
					color: #999;
					justify-content: center;
				}
			}
			
			.bottom{
				width: 100%;
				height: 20rpx;
			}
		}
	
		.opt_content{
			position: fixed;
			left: 50%;
			top: 50%;
			margin-left: -550/2rpx;
			margin-top: -800/2rpx;
			width: 550rpx;
			height: 800rpx;
			border-radius: 30rpx;
			background-color: #FFF;
			box-sizing: border-box;
			padding: 20rpx;
			
			.close{
				position: absolute;
				top: 20rpx;
				right: 20rpx;
				z-index: 1;
			}
			
			.title{
				position: relative;
				width: 100%;
				height: 100rpx;
				line-height: 100rpx;
				text-align: center;
				font-size: large;
			}
			
			.title_s{
				position: relative;
				font-size: small;
				color: #999;
				margin-top: 10rpx;
				margin-bottom: 40rpx;
			}
			
			.radio{
				position: relative;
				display: inline-block;
				font-size: x-small;
				color: #007AFF;
				border: solid 3rpx #007AFF;
				border-radius: 40rpx;
				padding: 15rpx 30rpx 15rpx 30rpx;
				margin: 0 20rpx 30rpx 0;
				
				&.on{
					color: #FFF;
					background-color: #007AFF;
				}
			}
			
			.pay_bar{
				position: absolute;
				bottom: 20rpx;
				width: 510rpx;
				height: 75rpx;
				display: flex;
				
				.price{
					display: flex;
					flex: 2.5;
					padding-left: 20rpx;
					align-items: center;
					color: #b50230;
				}
				.btn{
					display: flex;
					flex: 2;
					padding-top: 17rpx;
					justify-content: center;
					font-size: x-small;
					background-color: #007AFF;
					color: #fff;
					border-radius: 15rpx;
				}
			}
		}
	}
</style>
