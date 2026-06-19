<template>
	<view class="page">
		<view class="header">
			<view class="avatar">
				<text class="avatar-text">{{
					username.charAt(0).toUpperCase()
				}}</text>
			</view>
			<text class="username">{{ username }}</text>
		</view>

		<view class="menu">
			<view class="menu-item">
				<text class="menu-label">我的收藏</text>
				<text class="arrow">></text>
			</view>
			<view class="menu-item">
				<text class="menu-label">关于我们</text>
				<text class="arrow">></text>
			</view>
			<view class="menu-item">
				<text class="menu-label">帮助反馈</text>
				<text class="arrow">></text>
			</view>
		</view>

		<button class="btn-logout" @click="handleLogout">退出登录</button>
	</view>
</template>

<script>
export default {
	data() {
		return {
			username: "",
		};
	},
	onShow() {
		this.username = uni.getStorageSync("username") || "未登录";
	},
	methods: {
		handleLogout() {
			uni.showModal({
				title: "提示",
				content: "确定要退出登录吗？",
				success(res) {
					if (res.confirm) {
						uni.removeStorageSync("username");
						uni.reLaunch({
							url: "/pages/login/login",
						});
					}
				},
			});
		},
	},
};
</script>

<style>
.page {
	min-height: 100vh;
	background-color: #f5f5f5;
}

/* 头部 */
.header {
	background-color: #4A90D9;
	display: flex;
	flex-direction: column;
	align-items: center;
	padding: 80rpx 0 60rpx;
}

.avatar {
	width: 140rpx;
	height: 140rpx;
	border-radius: 50%;
	background-color: rgba(255, 255, 255, 0.3);
	display: flex;
	align-items: center;
	justify-content: center;
	margin-bottom: 24rpx;
}

.avatar-text {
	font-size: 56rpx;
	font-weight: bold;
	color: #ffffff;
}

.username {
	font-size: 32rpx;
	color: #ffffff;
}

/* 菜单 */
.menu {
	background-color: #ffffff;
	margin: 30rpx 24rpx;
	border-radius: 12rpx;
	overflow: hidden;
}

.menu-item {
	display: flex;
	align-items: center;
	justify-content: space-between;
	padding: 40rpx 36rpx;
	border-bottom: 1rpx solid #f0f0f0;
}

.menu-item:last-child {
	border-bottom: none;
}

.menu-label {
	font-size: 30rpx;
	color: #333333;
}

.arrow {
	font-size: 36rpx;
	color: #cccccc;
}

/* 退出按钮 */
.btn-logout {
	margin: 0 24rpx;
	height: 90rpx;
	background-color: #ffffff;
	color: #e74c3c;
	font-size: 30rpx;
	border-radius: 12rpx;
	border: none;
}
</style>