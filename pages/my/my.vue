<template>
	<view>
		<!--index.wxml-->
		<view class="user-container bg-white ">

			<view class="userinfo">
				<block>
					<view v-if="login">
						<view class="cu-avatar header-img round " :style="{backgroundImage: 'url(' + headerImg + ')' }"></view>
						<view class="userinfo-nickname text-center margin-top">
							<text>{{userName}}</text>
						</view>
					</view>
					<view v-else @tap="showLongModel">
						<view class="userinfo-avatar">
							<open-data type="userAvatarUrl" lang="zh_CN" />
						</view>
						<view class="userinfo-nickname text-center margin-top">
							<text>请登录</text>
						</view>
					</view>
				</block>

			</view>

		</view>

		<view class="cu-list menu  margin-top">
			<!-- <view v-if="ownerFlag== false" class="cu-item arrow" @click="bindingOwner()">
				<view class="content">
					<text class="cuIcon-profile text-pink"></text>
					<text class="text-grey">绑定业主</text>
				</view>
			</view> -->
			<view class="cu-item arrow" @click="viewOwner()">
				<view class="content">
					<text class="cuIcon-profile text-pink"></text>
					<text class="text-grey">业主信息</text>
				</view>
			</view>
			<view class="cu-item arrow" @click="myProperty()">
				<view class="content">
					<text class="cuIcon-service text-red"></text>
					<text class="text-grey">我的物业</text>
				</view>
			</view>
			<view class="cu-item arrow" @click="myComplaint()">
				<view class="content">
					<text class="cuIcon-form text-green"></text>
					<text class="text-grey">我的投诉单</text>
				</view>
			</view>
			<view class="cu-item arrow" @click="myHouse()">
				<view class="content">
					<text class="cuIcon-home text-blue"></text>
					<text class="text-grey">我的房屋</text>
				</view>
			</view>
			<view class="cu-item arrow" @click="myRepair()">
				<view class="content">
					<text class="cuIcon-formfill text-orange"></text>
					<text class="text-grey">我的报修单</text>
				</view>
			</view>
			
			<view class="cu-item arrow" @click="myParking()">
				<view class="content">
					<text class="cuIcon-formfill text-orange"></text>
					<text class="text-grey">车位信息</text>
				</view>
			</view>

			<view class="cu-item arrow" @click="mySettings()">
				<view class="content">
					<text class="cuIcon-settings text-gray"></text>
					<text class="text-grey">设置</text>
				</view>
			</view>

		</view>
	</view>
</template>

<script>
	/** index.js **/
	const context = require("../../context/Java110Context.js");
	const factory = context.factory; //获取app实例
	const constant = context.constant;
	//获取app实例
	const app = getApp().globalData;

	export default {
		data() {
			return {
				userInfo: {},
				headerImg: '',
				userName: '',
				// 用户信息
				ownerFlag: false, // 是否有业主信息 标记 如果有为 true  没有为false
				login: true

			};
		},

		components: {},
		props: {},
		onLoad: function(options) {
			context.onLoad(options);
			let _that = this;
			let login = context.checkLoginStatus();
			if (login) {
				factory.login.checkLoginStatus(function() {
					_that.userInfo = context.getUserInfo();
					_that.loadOwenrInfo();
				});
			}
		},
		onShow: function() {
			let _that = this; //查询用户信息

			if (!_that.ckeckUserInfo()) {
				_that.login = false;
				return;
			}
			_that.login = true;


			_that.loadOwenrInfo();
			_that.userInfo = context.getUserInfo();
			this.loadOwnerHeaderImg();
			// _that.setData({
			//   userInfo: context.getUserInfo()
			// });
		},
		methods: {
			bindingOwner: function() {
				if (!this.ckeckUserInfo()) {
					uni.navigateTo({
						url: '../showlogin/showlogin'
					});
					return;
				}
				wx.navigateTo({
					url: '../bindOwner/bindOwner'
				});
			},
			viewOwner: function() {
				if (!this.ckeckUserInfo()) {
					uni.navigateTo({
						url: '../showlogin/showlogin'
					});
					return;
				}
				wx.navigateTo({
					url: '../viewBindOwner/viewBindOwner'
				});
			},
			loadOwenrInfo: function() {
				let _that = this;

				context.getOwner(function(_ownerInfo) {
					console.log(_ownerInfo);

					if (_ownerInfo) {
						_that.ownerFlag = true;
						// _that.setData({
						//   ownerFlag: true
						// });
					} else {
						_that.ownerFlag = false;
						// _that.setData({
						//   ownerFlag: false
						// });
					}
				});
			},
			myProperty: function() {
				if (!this.ckeckUserInfo()) {
					uni.navigateTo({
						url: '../showlogin/showlogin'
					});
					return;
				}
				wx.navigateTo({
					url: '../myProperty/myProperty'
				});
			},

			myComplaint: function() {
				if (!this.ckeckUserInfo()) {
					uni.navigateTo({
						url: '../showlogin/showlogin'
					});
					return;
				}
				wx.navigateTo({
					url: '../complaintList/complaintList'
				});
			},

			onGotUserInfo: function(e) {
				console.log("nickname=" + JSON.stringify(e.detail.userInfo));
			},

			myHouse: function() {
				if (!this.ckeckUserInfo()) {
					uni.navigateTo({
						url: '../showlogin/showlogin'
					});
					return;
				}
				wx.navigateTo({
					url: '../my/myHouse'
				});
			},
			myRepair: function() {
				if (!this.ckeckUserInfo()) {
					uni.navigateTo({
						url: '../showlogin/showlogin'
					});
					return;
				}
				wx.navigateTo({
					url: '/pages/myRepair/myRepair',
				});
			},
			mySettings: function() {
				if (!this.ckeckUserInfo()) {
					uni.navigateTo({
						url: '../showlogin/showlogin'
					});
					return;
				}
				wx.navigateTo({
					url: '/pages/settings/settings',
				});
			},
			/**
			 * 查询业主头像
			 */
			loadOwnerHeaderImg: function() {
				let _that = this;
				context.getOwner(function(_owner) {
					let _headerImg = constant.url.getOwnerPhotoPath + "?objId=" + _owner.memberId + "&communityId=" + _owner.communityId +
						"&fileTypeCd=10000";
					_that.headerImg = _headerImg;
					_that.userName = _owner.appUserName;
				});
			},
			showLongModel: function() {
				uni.navigateTo({
					url: '../login/login'
				});
			},
			ckeckUserInfo: function() {
				return context.checkLoginStatus();
			},
			myParking:function(){
				wx.navigateTo({
					url: '/pages/parkingInfo/parkingInfo',
				});
			}

		}
	};
</script>
<style>
	@import "./my.css";
	
	.header-img{
		width: 200upx;
		height: 200upx;
	}
</style>
