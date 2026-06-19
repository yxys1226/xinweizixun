<template>
  <view class="page">
    <image class="cover" :src="news.cover" mode="aspectFill"></image>
    <view class="body">
      <text class="title">{{ news.title }}</text>
      <view class="meta">
        <text class="meta-text">{{ news.source }}</text>
        <text class="meta-text">{{ news.time }}</text>
      </view>
      <view class="divider"></view>
      <view class="content">{{ news.content }}</view>
    </view>
  </view>
</template>

<script>
import { newsList } from "../../data/news.js";

export default {
  data() {
    return {
      news: {},
    };
  },
  onLoad(options) {
    const id = Number(options.id);
    const found = newsList.find((item) => item.id === id);
    if (found) {
      this.news = found;
      uni.setNavigationBarTitle({
        title: found.source,
      });
    }
  },
};
</script>

<style>
.page {
    background-color: #ffffff;
    min-height: 100vh;
}

.cover {
    width: 100%;
    height: 420rpx;
}

.body {
    padding: 36rpx;
}

.title {
    font-size: 36rpx;
    font-weight: bold;
    color: #333333;
    line-height: 1.6;
}

.meta {
    display: flex;
    gap: 24rpx;
    margin-top: 20rpx;
}

.meta-text {
    font-size: 24rpx;
    color: #999999;
}

.divider {
    height: 1rpx;
    background-color: #eeeeee;
    margin: 30rpx 0;
}

.content {
    font-size: 30rpx;
    color: #555555;
    line-height: 1.9;
}
</style>