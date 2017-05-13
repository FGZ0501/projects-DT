<template>
	<div class="all">
		<div class='header' v-if='illogin'>
			<div class="container">
				<div class='company'>
					<div class='logo'><img src="../assets/homeShop/logo.jpg" alt="logo"></div>
					<p class='c_name'>信达</p>
					<div class='cityC'>
						<p class='city'>北京市</p>
						<p class='change' @click='changeCity'>[切换城市]</p>
					</div>
				</div>
				<div class='search'>
					<div class='search-t'><span :class='isproduct?"active":""' @click='isproduct=true'>产品</span><span :class='isproduct?"":"active"' @click='isproduct=false'>服务商</span></div>
					<div class='search-m'>
						<input class='searchBox' type="text" placeholder="搜索您需要的服务或服务商" v-model='searchName'>
						<div class="submit" @click='search'></div>
					</div>
					<div class='hotService'>热门服务：社保开户 公司注册</div>
				</div>
				<div class='telephone'><span></span><p>010-83421842</p></div>
			</div>
			<div class="tab">
				<div class="container">
					<ul>
						<li v-for='(catalog,index) in catalogs'>
							<router-link :to='catalog.src'>{{catalog.text}}</router-link>
						</li>
					</ul>
				</div> 
			</div>
		</div>
		<div class='header1' v-else>
			<div class="container">
				<div class="com">
					<div class="logo"><img src="../assets/homeShop/logo.jpg" alt="logo"></div>
					<p class='c_name'>信达</p>
					<p class='welcome'>{{validateTips}}</p>
				</div>
			</div>
		</div>

		<ul class="list" v-show='isHover'>
                    <li v-for="item in box" v-if='item.showOrder==1'>
                        <div>
                            <i></i>
                            <h1>{{item.name}}</h1>
                            <span v-for="i in item.itemList">{{i.name}}</span>
                        </div>
                        <ul class="list-tax">
                            <li v-for="i in item.itemList">
                                <h2> &nbsp; &nbsp;{{i.name}} > &nbsp;</h2>
                                <router-link to="/goodsList/财税服务">
                                    <div v-for="final in i.itemList">|&nbsp;&nbsp;{{final.name}}&nbsp;&nbsp;</div>
                                </router-link>
                            </li>
                        </ul>
                    </li>
                    <li v-for="item in box" v-if='item.showOrder==2'>
                        <div>
                            <i></i>
                            <h1>{{item.name}}</h1>
                            <span v-for="i in item.itemList">{{i.name}}</span>
                        </div>
                        <ul class="list-com">
                            <li v-for="i in item.itemList">
                                <h2> &nbsp; &nbsp;{{i.name}} > &nbsp;</h2>
                                <router-link to="/goodsLists/公司工商">
                                    <div v-for="final in i.itemList">|&nbsp;&nbsp;{{final.name}}&nbsp;&nbsp;</div>
                                </router-link>
                            </li>
                        </ul>
                    </li>
                    <li v-for="item in box" v-if='item.showOrder==3'>
                        <div>
                            <i></i>
                            <h1>{{item.name}}</h1>
                            <span v-for="i in item.itemList">{{i.name}}</span>
                        </div>
                        <ul class="list-ip">
                            <li v-for="i in item.itemList">
                                <h2> &nbsp; &nbsp;{{i.name}} > &nbsp;</h2>
                                <a v-for="final in i.itemList">|&nbsp;&nbsp;{{final.name}}&nbsp;&nbsp;</a>
                            </li>
                        </ul>
                    </li>
                    <li v-for="item in box" v-if='item.showOrder==4'>
                        <div>
                            <i></i>
                            <h1>{{item.name}}</h1>
                            <span v-for="i in item.itemList">{{i.name}}</span>
                        </div>
                        <ul class="list-ss">
                            <li v-for="i in item.itemList">
                                <h2> &nbsp; &nbsp;{{i.name}} > &nbsp;</h2>
                                <a v-for="final in i.itemList">|&nbsp;&nbsp;{{final.name}}&nbsp;&nbsp;</a>
                            </li>
                        </ul>
                    </li>
                </ul>
		<!--<div class="allProduct" v-show='isHover'>
			<div class="container">
				<ul class='productBar'>
					<li v-for='(product,index) in allProduct'  @mouseenter='disitems(index)'>
						<p class='productName'><span></span>{{product.name}}</p>
						<div class='productItem'><span v-for='item in product.itemList '>{{item.name}}</span></div>
						<div class="subItem" @mouseleave='hideItems(index)' v-show='disItems[index]'>
							<div v-for='item in product.itemList'>
								<p>{{item.name}}></p>
								<span v-for='i in item.itemList'>{{i.name}}</span>
							</div>
						</div>
					</li>
				</ul>
			</div>
		</div>-->
	</div>
</template>
<script>
import {mapGetters,mapActions} from 'vuex'
import ajax from 'axios'
	export default{
		data(){
			return{
				searchName:'',//搜索内容
				isproduct:true,//搜索类型
				allProduct:[],//全部产品
				disItems:{},
				catalogs:[
					{
						text:"全部产品",
						src:"/index"	
					},
					{
						text:"财税服务",
						src:"/goodsList/财税服务"	
					},
					{
						text:"公司工商",
						src:"/goodsLists/公司工商"	
					},
					{
						text:"加盟我们",
						src:"/joinUs"	
					},
					{
						text:"店铺",
						src:"/shop"	
					}
				],
                box: "",
                i: 0,
                hqs: [],
                products: [],
                providers: []
			}
		},
		created(){
			ajax.post('/xinda-api/product/style/list',{}).then(res=>{
				for(var attr in res.data.data){
					this.allProduct.push(res.data.data[attr])
				}
				this.allProduct.sort(this.compare('showOrder'))//将对象按照showOrder的大小进行排序
			});
			this.$parent.yc = true;
            ajax.post('/xinda-api/product/style/list', {}).then(res => {
                    this.box = res.data.data
                        //console.log(res.data.data);
                },
                err => {
                    console.log(err);
                });
		},
		watch:{//监测index进行页面跳转
			$route(to,from){
				if(to.path=="/index"){
					this.showAll(true);
				}else {
					this.showAll(false);
				}
			}
		},
		computed:{
			...mapGetters(['illogin','validateTips','isHover']),
			
		},
		methods:{
			...mapActions(['showAll']),
			// disAll(index){//展示全部产品分类
			// 	if(index==0){
			// 		this.showAll(true)
			// 	}
			// },
			disitems(i){
				this.$set(this.disItems,i,true)//给对象添加能被检测的属性
			},
			hideItems(i){
				this.$set(this.disItems,i,false)
			},
			getAjax(){
				//当前已选城市
				ajax.post('/xinda-api/common/select-region',{})
				.then(res=>{
					console.log(res)
					
				},err=>{
					console.log(err)
				})
			},
			changeCity(){//切换城市
				this.ajax.post("/xinda-api/common/change-region",{
					regionId: 110100					
				}).then(res=>{
					console.log(res)
				})
			},
			search(){
				if(this.searchName!=''){
					if(this.isproduct){
						//产品搜索
						this.ajax.post('/xinda-api/product/package/search-grid',{
								start:0,
								limit:8,
								searchName:this.searchName,
								sort:2
							}).then(res=>{
								console.log(res)
								//this.$router.push({path:'/goodsLists'})
							})
					}else{
						//服务商搜索
						this.ajax.post('/xinda-api/provider/search-grid',{
								start:0,
								limit:8,
								searchName:this.searchName,
								sort:1,
								productTypeCode:7,
								regionId:110105
							}).then(res=>{
								console.log(res)
							})
					}
				}
			},
		}
		
	}
</script>
<style lang='less' scoped>
.all {
	width: 1200px;
	margin: 0 auto;
	position: relative;
}
	.header {
		overflow: hidden;
		.company {
			margin-top: 39px;
			height: 56px;
			float: left;
			.cityC {
				float: left;
				margin-left: 28px;
				margin-top: 13px;
				.change {
					color: #2693d4;
					margin-top: 7px;
				}
			}
		}
		.search {
			margin-left: 111px;
			margin-top: 25px;
			float: left;
			.search-t {
				span:first-child {
					padding-right: 10px;
					border-right: 1px solid #2693d4;
				};
				span:last-child {
					padding-left: 10px;
				};
				span {
					cursor: pointer;
					&.active{
						color: #2693d4;
					}
				}
			}
			.search-m  {
				overflow: hidden;
				margin-top: 6px;
			}
			.searchBox {
				width: 481px;
				height: 39px;
				border: 2px solid #2693d4;
				display: block;
				float:left;
				font-size: 14px;
				padding-left: 20px;
			}
			input::-ms-input-placeholder{
				text-indent:10px;
			}
			input::-webkit-input-placeholder{
				text-indent:10px;
			}
			.submit {
				width: 103px;
				height: 43px;
				float: left;
				background: url(../assets/homeShop/search.jpg);
				background-size: cover;
				cursor: pointer;
			}
			.hotService {
				font-size: 12px;
				color: #c7c7c7;
				padding-left: 12px;
			}
			
		}
		.telephone {
			float: right;
			margin-top: 35px;
			line-height: 48px;
			span {
				width: 43px;
				height: 48px;
				display: inline-block;
				background: url(../assets/homeShop/homeshop-phone.gif) no-repeat;
				position: relative;
				margin-right: 4px;
			}
			p {
				float: right;
			}
		}
		.tab {
			border-bottom: 1px solid #2693d4;
			li {
				width: 77px;
				height: 34px;
				line-height: 32px;
				margin: 0 63px;
				text-align: center;
				float:left;
				font-size: 18px;
				box-sizing: border-box;
				a {
					display: block;
					color: #2a2a2a;
					
				}
				.router-link-active{
					border-bottom: 4px solid #2693d4;
					color: #2693d4;
				}
				
				
			}
		}
		
	}
	.logo {
		width: 52px;
		height: 100%;
		float: left;
		img {
			width: 100%;
			height: 100%;
		}
	}
	.c_name {
		float: left;
		margin-top: 13px;
		margin-left: 12px;
		font-family: 'FZDHTJW--GB1-0';
		font-weight: 900;
		font-size: 31px;
		color: #373737;
	}
	.header1 {
		padding-top: 25px;
		height: 98px;
		.com{
			overflow: hidden;
			.welcome {
				height: 56px;
				line-height: 56px;
				font-size: 17px;
				float:left;
				padding-left: 26px;
				margin-left: 38px;
				border-left: 1px solid #b4b4b4;
			}
		}
	}
	/*.allProduct{
		position: relative;
		z-index: 999;
		.productBar{
			width: 199px;
			height: 400px;
			background: #182a40;
			color: #ebeced;
			position: absolute;
			>li {
				position: relative;
				&:hover{
					background: #2693d4;
				};
				.subItem {
					width: 1001px;
					position: absolute;
					background: rgba(255,255,255,0.3);
					left: 199px;
					top: 0;
					height: 100%;
					div {
						margin-top: 9px;
						p {
							display: inline-block;
							padding-right: 9px;
							padding-left: 10px;
						}
					}
					span {
						padding: 0 10px;
						border-left: 1px solid #ebeced;
					}
				}
			}
			.productName {
				font-size: 16px;
				padding-left: 50px;
				padding-top: 16px;
				position: relative;
				span {
					width: 26px;
					height: 26px;
					display: inline-block;
					position: absolute;
					left: 14px;
				}
			}
			.productItem {
				padding-left: 50px;
				font-size: 14px;
				padding-bottom: 19px;
				span {
					margin-top: 10px;
					display: inline-block;
					&:nth-child(2n){
						margin-left: 17px;
					};
				}
			}
		}*/
	/*}*/
	 .list {
	 		position: absolute;
	 		left: 0;
            list-style: none;
            float: left;
            height: 400px;
            width: 199px;
            color: white;
            background-color: #132336;
            >li {
                position: relative;
                float: left;
                height: auto;
                /*116px*/
                width: 199px;
                &:hover {
                    background-color: #2693d4;
                    >ul {
                        display: block;
                    }
                }
                >div {
                    position: relative;
                    cursor: pointer;
                    height: auto;
                    width: 199px;
                    padding-left: 49px;
                    box-sizing: border-box;
                    float: left;
                    >h1 {
                        height: 52px;
                        font-size: 16px;
                        color: white;
                        font-family: "微软雅黑";
                        font-weight: normal;
                        padding-top: 20px;
                        box-sizing: border-box;
                    }
                    >i {
                        position: absolute;
                        top: 16px;
                        left: 14px;
                        display: block;
                        height: 24px;
                        width: 26px;
                        background-size: 100% 100%;
                    }
                    >span {
                        display: block;
                        float: left;
                        height: 32px;
                        width: 75px;
                        font-size: 14px;
                        color: white;
                        font-family: "微软雅黑";
                        font-weight: normal;
                    }
                }
                >ul {
                    position: absolute;
                    display: none;
                    left: 199px;
                    width: 1000px;
                    height: auto;
                    background-color: rgba(255, 255, 255, .4);
                    padding-top: 10px;
                    padding-bottom: 10px;
                    box-sizing: border-box;
                    z-index: 100;
                    >li {
                        clear: both;
                        list-style: none;
                        h2 {
                            float: left;
                            height: 32px;
                            font-size: 14px;
                            color: #FFFFFF;
                            font-family: "微软雅黑";
                            font-weight: 400;
                        }
                        a,a>div {
                            float: left;
                            display: block;
                            height: 25px;
                            font-size: 14px;
                            color: #FFFFFF;
                            font-family: "微软雅黑";
                            font-weight: 400;
                            cursor: pointer;
                        }
                    }
                }
            }
            >li:nth-child(1)>div>i {
                background-image: url(../assets/img/products-02.png);
            }
            >li:nth-child(2)>div>i {
                background-image: url(../assets/img/products-03.png);
            }
            >li:nth-child(3)>div>i {
                background-image: url(../assets/img/products-04.png);
            }
            >li:nth-child(4)>div>i {
                background-image: url(../assets/img/products-05.png);
            }
        }
</style>