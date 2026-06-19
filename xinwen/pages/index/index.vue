<template>
    <view class="page">
        <view class="tab-bar">
            <view v-for="(tab, index) in tabs" :key="index" class="tab-item" :class="{ active: currentTab === index }"
                @click="changeTab(index)">{{ tab }}
            </view>
        </view>

        <!-- 新闻列表 -->
        <view class="list">
            <view v-for="(item, index) in filteredList" :key="item.id" class="card" @click="goDetail(item.id)">
                <image class="cover" :src="item.cover" mode="aspectFill"></image>
                <view class="info">
                    <text class="title">{{ item.title }}</text>
                    <view class="meta">
                        <text class="meta-text">{{ item.source }}</text>
                        <text class="meta-text">评论 {{ item.commentCount }}</text>
                        <text class="meta-text">{{ item.time }}</text>
                    </view>
                </view>
                <text class="close" @click.stop="removeItem(index)">×</text>
            </view>

            <view v-if="filteredList.length === 0" class="empty">
                <text>暂无内容</text>
            </view>
        </view>

        <view v-if="showBackTop" class="back-top" @click="scrollToTop">
            <text class="back-top-text">↑</text>
        </view>
    </view>

</template>

<script>
import { tabs, newsList } from '../../data/news'

export default {
    data() {
        return {
            tabs,
            list: [...newsList],
            currentTab: 0,
            showBackTop: false,
            currentScrollTop: 0,
            backTopThreshold: 0,
        };
    },
    computed: {
        filteredList() {
            if (this.currentTab === 0) return this.list;
            const category = this.tabs[this.currentTab];
            return this.list.filter((item) => item.category === category);
        },
    },
    onReady() {
        this.updateBackTopThreshold();
    },
    onPageScroll(event) {
        this.currentScrollTop = event.scrollTop;
        this.showBackTop =
            this.backTopThreshold > 0 && event.scrollTop >= this.backTopThreshold;
    },

    methods: {
        changeTab(index) {
            if (this.currentTab === index) return;
            const shouldScrollTop = this.currentScrollTop > 0;
            this.currentTab = index;
            this.showBackTop = false;
            this.currentScrollTop = 0;
            this.$nextTick(() => {
                if (shouldScrollTop) {
                    uni.pageScrollTo({
                        scrollTop: 0,
                        duration: 0,
                        fail: () => { },
                    });
                }
                setTimeout(() => {
                    this.updateBackTopThreshold();
                }, 60);
            });

        },
        removeItem(index) {
            const category = this.tabs[this.currentTab];
            if (this.currentTab === 0) {
                this.list.splice(index, 1);
            } else {
                const target = this.filteredList[index];
                const i = this.list.findIndex((item) => item.id === target.id);
                if (i !== -1) this.list.splice(i, 1);
            }
            this.updateBackTopThreshold();
        },
        updateBackTopThreshold() {
            this.$nextTick(() => {
                const query = uni.createSelectorQuery().in(this);
                query
                    .select(".card")
                    .boundingClientRect((rect) => {
                        if (!rect) {
                            this.backTopThreshold = 0;
                            this.showBackTop = false;
                            return;
                        }
                        this.backTopThreshold =
                            rect.top + this.currentScrollTop + rect.height;
                    })
                    .exec();
            });
        },
        scrollToTop() {
            uni.pageScrollTo({
                scrollTop: 0,
                duration: 260,
                fail: () => { },
            });
            this.showBackTop = false;
        },
        goDetail(id) {
            uni.navigateTo({
                url: `/pages/detail/detail?id=` + id,
            });
        },
    },
};

</script>

<style>
.page {
    background-color: #f5f5f5;
}

/* Tab */
.tab-bar {
    display: flex;
    align-items: stretch;
    background-color: #ffffff;
    border-bottom: 1rpx solid #eeeeee;
    flex-shrink: 0;
}

.tab-item {
    flex: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    height: 88rpx;
    font-size: 28rpx;
    color: #666666;
    border-bottom: 4rpx solid transparent;
    box-sizing: border-box;
}

.tab-item.active {
    color: #4A90D9;
    border-bottom-color: #4A90D9;
    font-weight: bold;
}

/* 卡片 */
.card {
    display: flex;
    align-items: center;
    background-color: #ffffff;
    margin: 16rpx 24rpx;
    border-radius: 12rpx;
    padding: 18rpx;
    position: relative;
}

.cover {
    width: 200rpx;
    height: 140rpx;
    border-radius: 8rpx;
    flex-shrink: 0;
}

.info {
    flex: 1;
    padding: 0 20rpx;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    height: 140rpx;
}

.title {
    font-size: 28rpx;
    color: #333333;
    line-height: 1.5;
    display: -webkit-box;
    -webkit-line-clamp: 3;
    -webkit-box-orient: vertical;
    overflow: hidden;
}

.meta {
    display: flex;
    gap: 16rpx;
}

.meta-text {
    font-size: 22rpx;
    color: #999999;
}

/* 关闭按钮 */
.close {
    position: absolute;
    top: 12rpx;
    right: 0;
    font-size: 36rpx;
    color: #cccccc;
    line-height: 1;
}

/* 空状态 */
.empty {
    text-align: center;
    padding: 100rpx 0;
    color: #cccccc;
    font-size: 28rpx;
}

.back-top {
    position: fixed;
    right: 30rpx;
    bottom: calc(128rpx + env(safe-area-inset-bottom));
    width: 72rpx;
    height: 72rpx;
    border-radius: 50%;
    background-color: rgba(255, 255, 255, 0.92);
    border: 1rpx solid rgba(74, 144, 217, 0.22);
    box-shadow: 0 10rpx 24rpx rgba(43, 92, 139, 0.12);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 20;
}

.back-top:active {
    background-color: #f3f8fd;
    transform: translateY(2rpx);
}

.back-top-arrow {
    color: #4A90D9;
    font-size: 34rpx;
    font-weight: 600;
    line-height: 1;
}
</style>
