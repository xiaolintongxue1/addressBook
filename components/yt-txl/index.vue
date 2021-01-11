<template>
	<view class="yt-txl-container">
		<view class="search">
			<input type="text" @input="inputFunc" class="s-input" placeholder="搜索" />
		</view>
		<view class="dropdown">
			<tui-dropdown-list class="dropdownList" :show="dropdownShow" :top="94" :height="200">
				<template class="dropdownSelect" v-slot:selectionbox>
					<tui-button type="white" shape="circle" @click="showDropDownList">
						{{companyName}}
						<view class="tui-animation" :class="[dropdownShow?'tui-animation-show':'']">
							<tui-icon name="turningdown" :size="20"></tui-icon>
						</view>
					</tui-button>
				</template>
				<template class="dropdownBox"  v-slot:dropdownbox>
					<view class="tui-selected-list">
						<scroll-view scroll-y class="tui-dropdown-scroll">
							<block v-for="(item,index) in sResource" :key="index">
								<tui-list-cell padding="0" @click="dropDownList(item[0].data[0].companyId)" :unlined="dropdownlistData.length-1==index">
									<view class="tui-cell-class">
										<text class="tui-ml-20">{{item[0].data[0].companyName}}</text>
									</view>
								
								</tui-list-cell>
							</block>
						</scroll-view>
					</view>
				</template>
			</tui-dropdown-list>
		</view>
		<view class="scroll" :style="index ? '' :'right:25upx'">
			<scroll-view :scroll-into-view="to" scroll-y style="width: 100%;height: 100%;">
				<view :id="o.key" v-for="(o,i) in resource" :key="i">
					<view class="p" >{{o.key}}</view>
					<view @click="clickFunc(item)" v-for="(item,index) in o.data" class="info" :key="index">
						<view :style="'background:'+color" class="icon">
							{{item[name].slice(0,1)}}
						</view>
						<view class="item">
							<text class="name">{{item[name]}}</text>
							<text class="post">{{item.postName}}</text>
						</view>
					</view>
				</view>
			</scroll-view>
		</view>
		<view class="flag" v-if="index">
			<scroll-view scroll-y="true" :show-scrollbar="false" class="flag-scroll" style="width: 100%;height: 100%;">
				<view @click="toFunc(o.key)" class="flag-key" v-for="(o,i) in resource" :key="i">
					{{o.key}}
				</view>
			</scroll-view>
		</view>
	</view>
</template>

<script>
	'use strict';

	let pinyin = new(require('./pinyin'))({
		charCase: 0
	});
	import tuiDropdownList from "@/components/tui-dropdown-list/tui-dropdown-list"
	import tuiButton from "@/components/tui-button/tui-button"
	import tuiIcon from "@/components/tui-icon/tui-icon.vue"
	import tuiListCell from "@/components/tui-list-cell/tui-list-cell"
	export default {
		components:{
			tuiButton,
			tuiIcon,
			tuiDropdownList,
			tuiListCell
		},
		props: {
			datas: {
				type: Array,
				default () {
					return [];
				}
			},
			name: {
				type: String,
				default () {
					return "name";
				}
			},
			index: {
				type: Boolean,
				default: true
			},
			color: {
				type:String,
				default:"#f44336"
			}
		},
		data() {
			return {
				companyName:"",
				sResource:{},
				resource: [],
				chars: [],
				to: "",
				cache:[],
				dropdownShow:false,
				dropdownlistData: [{
					name: "总公司",
					icon: "wechat",
					color: "#80D640",
					size: 15
				}, {
					name: "分公司",
					icon: "alipay",
					color: "#00AAEE",
					size: 15
				},],
			};
		},
		watch: {
			datas(r) {
				if (!(r instanceof Array)) {
					console.log("the props datas type must be array")
					return
				}
				this._parseData(r)
			}
		},
		methods: {
			showDropDownList(){
				this.dropdownShow = !this.dropdownShow
			},
			dropDownList(index) {
				console.log(index,this.sResource)
				this.resource=this.sResource[index];
				this.cache=this.resource;
				uni.setStorageSync('resource', this.resource);
				this.companyName=this.resource[0].data[0].companyName;
				this.dropdownShow = !this.dropdownShow
			},
			toFunc(o) {
				this.to = o
			},
			clickFunc(item){
				uni.setStorageSync('employeeId', item.employeeId);
				uni.navigateTo({
				    url: '../../pages/personInfo/personInfo'
				});
				
			},
			inputFunc(r){ //搜索功能
				if(!r.detail.value) {
					this.resource = this.cache
					return
				}
				
				let temp = []
				this.cache.forEach(o => {
					o.data.forEach(item =>{
						if(item[this.name].indexOf(r.detail.value) > -1){ // 匹配到
							// 确定当前这个元素的key是谁
							let key = o.key
							// 找到temp中的key
							let index = 0 //下标
							let find = false // 数据是否存在
							for(let d in temp){
								if(temp[d].key === o.key){
									index = d
									find = true
								}
							}
							if(find){ // 如果key已经存在，直接插入数据
								temp[index].data.push(item)
							} else { // 不存在初始化一个并存入
								temp.push({
									key:o.key,
									data:[item]
								})
							}
						}
					})
				})
				this.resource = temp
			},
			_type(val){
				return Object.prototype.toString.call(val).slice(8,-1).toLowerCase()
			},
			_isExist(item){
				for (var i = 0; i < this.chars.length; i++) {
					if(this.chars[i]==item){
						return false;
					}
				}
				return true;
			},
			_parseData(r) {
				// 生成a-z的数组
				let data = [];
				this.chars = [];
				for (var i = 0; i < r.length; i++) {
					if(this._isExist(r[i].departmentName)){
						data.push({"key":r[i].departmentName,data:[]});
						this.chars.push(r[i].departmentName)
					}
				}
				/* for (let i = 65; i <= 90; i++) {
					let key = String.fromCharCode(i)
					data.push({"key":key,data:[]})
					this.chars.push(key)
				} */
				if(this._type(r) === "array") {
					
					// 填充数据
					r.forEach(o => {
						// 找到char的位置
						data.forEach( (item,index) => {
							//let a = this._parseChar(o[this.name || 'name'])
							let a=o.departmentName;
							if(item.key === a){
								data[index].data.push(o)
							}
						})
					})
				}
				// 组合最后数据并踢出没有匹配到a-z中的任意数据
				let finalData = []
				for (let i in data) {
					if(data[i].data.length > 0){
						finalData.push(data[i])
					}
				}
				this.resource = finalData
				this.cache = finalData
				finalData = null
			},
			_parseResource(r){
				// 生成a-z的数组
				let data = [];
				this.chars = [];
				for (var i = 0; i < r.length; i++) {
					if(this._isExist(r[i].departmentName)){
						data.push({"key":r[i].departmentName,data:[]});
						this.chars.push(r[i].departmentName)
					}
				}
				/* for (let i = 65; i <= 90; i++) {
					let key = String.fromCharCode(i)
					data.push({"key":key,data:[]})
					this.chars.push(key)
				} */
				if(this._type(r) === "array") {
					
					// 填充数据
					r.forEach(o => {
						// 找到char的位置
						data.forEach( (item,index) => {
							//let a = this._parseChar(o[this.name || 'name'])
							let a=o.departmentName;
							if(item.key === a){
								data[index].data.push(o)
							}
						})
					})
				}
				// 组合最后数据并踢出没有匹配到a-z中的任意数据
				let finalData = []
				for (let i in data) {
					if(data[i].data.length > 0){
						finalData.push(data[i])
					}
				}
				return finalData;
			},
			_parseChar(name) {
				if (Object.prototype.toString.call(name).slice(8, -1) !== 'String') {
					console.error("name is not string")
					return
				}
				let chars = pinyin.getFullChars(name);
				return chars[0].toUpperCase()
			},
			classifyByCompanyId(){
				let id={},sRescource={};
				let resource=this.resource;
				let companyId=resource[0].data[0].companyId;
				for (let i = 0; i < resource.length; i++) {
					for (let j = 0; j < resource[i].data.length; j++) {
						if(sRescource[resource[i].data[j].companyId]){
							sRescource[resource[i].data[j].companyId].push(resource[i].data[j])
						}
						else{
							sRescource[resource[i].data[j].companyId]=[resource[i].data[j]]
						}
					}
				}
				for (let item in sRescource) {
					sRescource[item]=this._parseResource(sRescource[item]);
				}
				this.sResource=sRescource;//字母写错
				this.resource=this.sResource[companyId];
				this.cache=this.resource;
				this.companyName=this.resource[0].data[0].companyName;
				uni.setStorageSync('resource', this.resource);
			}
		},
		mounted() {
			this._parseData(this.datas)
			this.classifyByCompanyId();
		}
	};
</script>

<style>
	page {
		background: #f4f4f4;
	}

	.search {
		width: 100%;
		height: 120upx;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.s-input {
		width: 700upx;
		height: 80upx;
		background: url('data:image/png;base64,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') 20upx center no-repeat #fff;
		background-size: 40upx;
		text-indent: 80upx;
	}

	.scroll {
		position: absolute;
		left: 25upx;
		top: 100px;
		right: 100upx;
		bottom: 25upx;
	}

	.p {
		position: sticky;
		top: 0;
		left: 0;
		background: #f4f4f4;
		font-size: 28upx;
		margin-bottom: 10upx;
		text-indent: 40upx;
		z-index: 100;
		font-weight: bold;
	}

	.info {
		display: flex;
		justify-content: flex-start;
		align-items: center;
		margin-bottom: 10upx;
		padding: 20upx 25upx;
		background: #fff;
	}

	.icon {
		width: 100upx;
		height: 100upx;
		border-radius: 50%;
		margin-right: 20upx;
		display: flex;
		justify-content: center;
		align-items: center;
		font-size: 30upx;
		color: #fff;
	}

	.item {
		height: 100upx;
		display: flex;
		flex-direction: column;
		justify-content: space-around;
	}

	.flag {
		width: 50upx;
		position: absolute;
		top: 120upx;
		right: 25upx;
		bottom: 25upx;
	}

	.flag-scroll {
		padding-top: 10upx;
	}

	.flag-key {
		padding: 0;
		margin: 0 auto 10upx auto;
		width: 30upx;
		height: 30upx;
		border-radius: 50%;
		color: #000;
		font-weight: bold;
		display: flex;
		justify-content: center;
		align-items: center;
		font-size: 20upx;
	}
	.dropdown{
		position: absolute;
		left: 25upx;
		top: 120upx;
		right: 100upx;
		bottom: 25upx;
	}
	
	
	
	//下拉框样式修改
	.tui-cell-class{
		z-index: 99999;
	}
	.dropdown{
		position: static !important;
		margin-left: 15px;
		margin-right: 15px;
	}
	.tui-btn{
		height: 40px !important;
		line-height:40px !important ;
		text-align: center;
	}
	
	
	
	
	
	
	
	//下拉框样式
	/* 隐藏scroll-view滚动条*/
	::-webkit-scrollbar {
		width: 0;
		height: 0;
		color: transparent;
	}
	
	/*header*/
	.tui-header {
		width: 100%;
		padding-top: 34rpx;
		/* box-shadow: 0 15rpx 10rpx -15rpx #f2f2f2; */
		box-sizing: border-box;
		background-color: #fff;
		position: fixed;
		z-index: 1000;
	}
	
	.tui-header-top,
	.tui-header-bottom {
		display: flex;
		align-items: center;
		justify-content: space-between;
		font-size: 26rpx;
		color: #333;
	}
	
	.tui-top-item {
		height: 26rpx;
		line-height: 26rpx;
		flex: 1;
		display: flex;
		align-items: center;
		justify-content: center;
	}
	
	.tui-topitem-active {
		position: relative;
		font-weight: bold;
	}
	
	.tui-topitem-active::after {
		content: '';
		position: absolute;
		width: 44rpx;
		height: 6rpx;
		background: #5677fc;
		border-radius: 6rpx;
		bottom: -10rpx;
		left: 50%;
		-webkit-transform: translateX(-50%);
		transform: translateX(-50%);
	}
	
	.tui-price-arrow {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		height: 20rpx;
	}
	
	.tui-bottom-item .tui-icon-class,
	.tui-screen .tui-icon-class {
		margin-left: 6rpx;
	}
	
	.tui-icon-box {
		line-height: 12px !important;
		padding: 0 !important;
		display: block !important;
		position: relative;
	}
	
	.tui-arrow-up {
		top: 5px;
	}
	
	.tui-arrow-down {
		top: -3px;
	}
	
	.tui-header-bottom {
		margin-top: 56rpx;
		height: 108rpx;
		padding: 0 30rpx;
		box-sizing: border-box;
		font-size: 24rpx;
		align-items: flex-start;
		overflow: hidden;
	}
	
	.tui-bottom-text {
		line-height: 24rpx;
	}
	
	.tui-bottom-item {
		flex: 1;
		display: inline-flex;
		align-items: center;
		justify-content: center;
		padding: 18rpx 12rpx;
		border-radius: 40rpx;
		box-sizing: border-box;
		background: #f2f2f2;
		margin-right: 20rpx;
		white-space: nowrap;
	}
	
	.tui-bottom-item:last-child {
		margin-right: 0;
	}
	
	.tui-btmItem-active {
		padding-bottom: 60rpx;
		border-bottom-left-radius: 0;
		border-bottom-right-radius: 0;
	}
	
	.tui-bold {
		font-weight: bold;
	}
	
	.tui-active {
		color: #5677fc;
	}
	
	.tui-ml {
		margin-left: 6rpx;
	}
	
	.tui-seizeaseat-20 {
		height: 20rpx;
	}
	
	.tui-seizeaseat-30 {
		height: 30rpx;
	}
	
	.tui-middle {
		vertical-align: middle;
	}
	
	.tui-drop-item .tui-icon-class {
		vertical-align: middle;
	}
	
	/*header*/
	
	/*header 下拉选择*/
	
	.tui-scroll-box {
		width: 100%;
		height: 480rpx;
		box-sizing: border-box;
		position: relative;
		z-index: 99;
		color: #fff;
		font-size: 30rpx;
		word-break: break-all;
	}
	
	.tui-drop-item {
		color: #333;
		height: 80rpx;
		font-size: 28rpx;
		padding: 20rpx 40rpx 20rpx 40rpx;
		box-sizing: border-box;
		display: inline-block;
		width: 50%;
	}
	
	.tui-drop-btnbox {
		width: 100%;
		height: 100rpx;
		position: absolute;
		left: 0;
		bottom: 0;
		box-sizing: border-box;
		display: flex;
	}
	
	.tui-drop-btn {
		width: 50% !important;
		border-radius: 0 !important;
		font-size: 32rpx !important;
		text-align: center;
		height: 100rpx;
		line-height: 100rpx;
		border: 0;
	}
	
	
	/*header 下拉选择*/
	
	.top-dropdown {
		margin-top: 360rpx;
		padding: 0 40rpx;
		box-sizing: border-box;
	}
	
	.tui-share-box {
		padding: 0 50rpx;
		box-sizing: border-box;
	}
	
	.tui-drop-input-box {
		padding: 50rpx;
		box-sizing: border-box;
	}
	
	.tui-animation {
		display: inline-block;
		transform: rotate(0deg);
		transition: all 0.2s;
	}
	
	.tui-animation-show {
		transform: rotate(180deg);
	}
	
	.tui-selected-list {
		background-color: #fff;
		border-radius: 20rpx;
		overflow: hidden;
		transform: translateZ(0);
	}
	
	.tui-dropdown-scroll {
		height: 400rpx;
	}
	
	.tui-cell-class {
		display: flex;
		align-items: center;
		padding: 26rpx 30rpx !important;
	}
	
	.tui-ml-20 {
		margin-left: 20rpx;
	}
	
	.tui-share {
		background: #e8e8e8;
		position: relative;
		padding-bottom: env(safe-area-inset-bottom);
	}
	
	.tui-share-title {
		font-size: 26rpx;
		color: #7E7E7E;
		text-align: center;
		line-height: 26rpx;
		padding: 20rpx 0 50rpx 0;
	}
	
	.tui-share-top,
	.tui-share-bottom {
		min-width: 101%;
		padding: 0 20rpx 0 30rpx;
		white-space: nowrap;
	}
	
	.tui-mt {
		margin-top: 30rpx;
		padding-bottom: 150rpx;
	}
	
	.tui-share-item {
		width: 126rpx;
		display: inline-block;
		margin-right: 24rpx;
		text-align: center;
	}
	
	.tui-item-last {
		margin: 0;
	}
	
	.tui-empty {
		display: inline-block;
		width: 30rpx;
		visibility: hidden;
	}
	
	.tui-share-icon {
		display: flex;
		align-items: center;
		justify-content: center;
		background: #fafafa;
		height: 126rpx;
		width: 126rpx;
		border-radius: 32rpx;
	}
	
	.tui-share-text {
		font-size: 24rpx;
		color: #7E7E7E;
		line-height: 24rpx;
		padding: 20rpx 0;
		white-space: nowrap;
	}
	
	.tui-btn-cancle {
		width: 100%;
		height: 100rpx;
		position: absolute;
		left: 0;
		bottom: 0;
		background: #f6f6f6;
		font-size: 36rpx;
		color: #3e3e3e;
		display: flex;
		align-items: center;
		justify-content: center;
		padding-bottom: env(safe-area-inset-bottom);
	}
	
	.tui-hover {
		background: rgba(0, 0, 0, 0.2)
	}
</style>
