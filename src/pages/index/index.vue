<!-- 使用 type="home" 属性设置首页，其他页面不需要设置，默认为page；推荐使用json5，更强大，且允许注释 -->
<route lang="json5" type="home">
{
  style: {
    navigationBarTitleText: '扫码点餐',
    navigationBarBackgroundColor: '#FFF',
  },
}
</route>
<template>
  <view
    class="bg-white overflow-hidden pt-2 px-3"
    :style="{ marginTop: safeAreaInsets?.top + 'px' }"
    id="content"
  >
    <!--
      <image src="/static/logo.svg" alt="" class="w-28 h-28 block mx-auto" />
    </view>
    <view class="text-center text-4xl main-title-color mt-4">unibest</view>
    <view class="text-center text-2xl mt-2 mb-8">最好用的 uniapp 开发模板</view>

    <view class="text-justify max-w-100 m-auto text-4 indent mb-2">{{ description }}</view>
    <view class="text-center mt-8">
      当前平台是：
      <text class="text-green-500">{{ PLATFORM.platform }}</text>
    </view>
    <view class="text-center mt-4">
      模板分支是：
      <text class="text-green-500">base</text>
    </view> -->
    <view class="home-search mb-4">
      <wd-icon class="search-icon" name="search" size="14px"></wd-icon>
      <input
        class="home-search-input"
        placeholder="请输入商品名称"
        v-model="searchValue"
        @input="handleChangeValue"
      />
      <wd-icon name="list" class="home-search-icon" size="22px" color="rgb(161 161 161)"></wd-icon>
    </view>

    <view class="home-desk mb-2">
      <view class="home-shop">
        <text class="home-shop-text">我的店铺</text>
        <wd-icon name="arrow-right" size="18px"></wd-icon>
      </view>
      <view>桌号: A0001</view>
    </view>
  </view>
  <view class="lien"></view>
  <view class="home-dish" :style="dynamicStyle">
    <view class="wraper">
      <wd-sidebar v-model="active" @change="handleChange">
        <wd-sidebar-item
          v-for="(item, index) in categories"
          :key="index"
          :value="index"
          :label="item.label"
        />
      </wd-sidebar>
      <scroll-view
        class="content"
        scroll-y
        scroll-with-animation
        :scroll-top="scrollTop"
        :throttle="false"
        @scroll="onScroll"
      >
        <view v-for="(item, index) in categories" :key="index" class="category">
          <!-- <wd-cell-group :title="item.title" border>
            <wd-cell
              v-for="(cell, index) in item.items"
              :key="index"
              :title="cell.title"
              :label="cell.label"
            >
              <wd-icon name="github-filled" size="24px"></wd-icon>
            </wd-cell>
          </wd-cell-group> -->
        </view>
      </scroll-view>
    </view>
  </view>
</template>

<script lang="ts" setup>
import { TestEnum } from '@/typings'
import PLATFORM from '@/utils/platform'
import { onMounted, ref } from 'vue'
import { getRect, isArray } from 'wot-design-uni/components/common/util'

defineOptions({
  name: 'Home',
})

// 获取屏幕边界到安全区域距离
const { safeAreaInsets } = uni.getSystemInfoSync()

// 测试 uni API 自动引入
onLoad(() => {})

const contentHeight = ref<number>(0)

const dynamicStyle = ref({
  '--primary-color': 200 + 'rpx',
})

onMounted(() => {
  const query = uni.createSelectorQuery().in(this)
  query
    .select('#content') // 选择类名为 .content 的元素
    .boundingClientRect((rect) => {
      if (rect) {
        const result = rect as UniApp.NodeInfo
        contentHeight.value = result.height
      }
    })
    .exec()
})
const searchValue = ref<string>('')
const handleChangeValue = () => {
  console.log('123')
}

const active = ref<number>(1)
const scrollTop = ref<number>(0)
const itemScrollTop = ref<number[]>([])

const subCategories = new Array(24).fill({ title: '标题文字', label: '这是描述这是描述' }, 0, 24)
const categories = ref([
  {
    label: '蒸菜',
    title: '标题一',
    items: subCategories,
  },
  {
    label: '卤菜',
    title: '标题二',
    items: subCategories,
  },
  {
    label: '香菜',
    title: '标题三',
    items: subCategories.slice(0, 18),
  },
  {
    label: '川菜',
    title: '标题四',
    items: subCategories.slice(0, 21),
  },
  {
    label: '蒸菜',
    title: '标题一',
    items: subCategories,
  },
  {
    label: '卤菜',
    title: '标题二',
    items: subCategories,
  },
  {
    label: '香菜',
    title: '标题三',
    items: subCategories.slice(0, 18),
  },
  {
    label: '川菜',
    title: '标题四',
    items: subCategories.slice(0, 21),
  },
])

onMounted(() => {
  getRect('.category', true).then((rects) => {
    if (isArray(rects)) {
      itemScrollTop.value = rects.map((item) => item.top || 0)
      scrollTop.value = rects[active.value].top || 0
    }
  })
})

function handleChange({ value }) {
  active.value = value
  scrollTop.value = itemScrollTop.value[value]
}
function onScroll(e) {
  const { scrollTop } = e.detail
  const threshold = 50 // 下一个标题与顶部的距离
  if (scrollTop < threshold) {
    active.value = 0
    return
  }
  const index = itemScrollTop.value.findIndex(
    (top) => top > scrollTop && top - scrollTop <= threshold,
  )
  if (index > -1) {
    active.value = index
  }
}
</script>

<style lang="scss">
.main-title-color {
  color: #d14328;
}

.text-input {
  padding: 10rpx;
}

.home-search {
  position: relative;
  display: flex;
  align-items: center;
  width: 100%;
  height: 100%;
}
.search-icon {
  position: absolute;
  top: 18rpx;
  left: 18rpx;
}
.home-search-input {
  flex: 1; /* 自适应宽度 */
  height: 36rpx;
  padding: 12rpx;
  padding-left: 50rpx;
  font-size: 28rpx;
  background: #f7f7f7;
  border-radius: 100px;
}

.home-search-icon {
  width: 40rpx;
  margin-left: 20rpx;
}

.home-shop {
  display: flex;
  align-items: center;
  line-height: 20rpx;
}

.home-desk {
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 34rpx;
}
.lien {
  width: 100%;
  height: 16rpx;
  background: #f7f7f7;
}
.wraper {
  display: flex;
  height: calc(100vh - var(--window-top) - var(--primary-color) - var(--window-bottom));
}

.content {
  flex: 1;
  background: #fff;
}
</style>
