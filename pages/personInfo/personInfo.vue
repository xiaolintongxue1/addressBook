<template>
	<view class="pContainer">
		<view class="pItem pHead">
			<view class="icon">
				{{data.employeeName.slice(0,1)}}
			</view>
			<view class="name">
				{{data.employeeName}}
			</view>
			<view class="font-icon">
				<text class="lg text-gray cuIcon-favor"></text>
			</view>
		</view>
		<view class="pItem">
			<view class="telNumber">{{data.tel}}</view>
			<view class="telIcon">
				<text class="lg text-gray cuIcon-phone" @click="callPhone(data.tel)"></text>
			</view>
		</view>       
		<view class="pItem ">
			<view class="pText">
				{{data.departmentName}}
			</view>
		</view>
		<view class="pItem ">
			<view class="pText">
				{{data.postName}}
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				data:{}
			}
		},
		methods: {
			callPhone(telNumber){
				uni.makePhoneCall({
				 	
				 	// 手机号
				    phoneNumber: telNumber, 
				
					// 成功回调
					success: (res) => {
						console.log('调用成功!')	
					},
				
					// 失败回调
					fail: (res) => {
						console.log('调用失败!')
					},
				})
			},
			getData(){
				let resource=uni.getStorageSync("resource");
				let o=uni.getStorageSync("employeeId");
				console.log(o)
				let flag=false;
				for (var i = 0; i < resource.length; i++) {
					for (var j = 0; j < resource[i].data.length; j++) {
						if(o==resource[i].data[j].employeeId){
							this.data=resource[i].data[j];
							flag=true;
							break;
						}
					}
					if(flag)break;
				}
			}
		},
		onShow() {
			this.getData();
		}
	}
</script>

<style scoped>
	.pContainer{
		width: 100%;
		height: 100%;
		margin: 0;
		padding: 0;
		background-color: #f0f0f0;
	}
	.pItem{
		margin: 25px 0 0 0;
		height: 46px;
		background-color: #F8F8F8;
		display: flex;
		flex-direction: row;
		align-items: center;
		font-size:14px;
	}
	.icon {
		width: 40px;
		height: 40px;
		border-radius: 50%;
		margin-right: 20upx;
		margin-left:20upx;
		display: flex;
		justify-content: center;
		align-items: center;
		font-size: 30upx;
		color: #fff;
		background-color: #ff0000 ;
	}
	.name{
		margin-left: 25px;
	}
	.font-icon{
		flex-grow:3;
		display: flex;
		justify-content: flex-end;
		margin-right: 15px;
	}
	 text{
		font-size: 18px;
	}
	.telNumber{
		margin-left: 20upx;
	}
	.telIcon{
		flex-grow: 4;
		display: flex;
		justify-content: flex-end;
	}
	.telIcon text{
		margin-right: 15px;
	}
	.pText{
		margin-left: 15px;
	}
</style>
