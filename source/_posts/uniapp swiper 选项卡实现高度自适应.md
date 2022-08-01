---
title: uniapp swiper 选项卡实现高度自适应
date: 2022-08-01
---


> https://uviewui.com/components/image.html


```vue
<template>
    <view class="body-div">
      <view class="_tabs_p">
        <u-tabs :list="tabsList" :is-scroll="false" :current="tabsCurrent" @change="change" font-size="32" height="100">
        </u-tabs>
      </view>
      <swiper class="swiper" :current="tabsCurrent" @change="swiperChange" :style="{'height': swiperHeight+'px'}">
        <swiper-item>
          <view class="list__ list0" > </view>
        </swiper-item>
        <swiper-item>
          <view class="list__ list1" > </view>
        </swiper-item>
      </swiper>
    </view>
</template>

<script>

  export default {

    data() {
      return {
        swiperHeight: 0,
        tabsCurrent: 0,
      }
    },

    mounted() {
      this.initSwiperHeight('.list0')
    },
    methods: {
      initSwiperHeight(list) {
        const query = uni.createSelectorQuery().in(this);
        query.select(list).boundingClientRect(data => {
          this.swiperHeight = data.height + 20
        }).exec();
      },
      change(index) {
        this.tabsCurrent = index;
        this.initSwiperHeight('.list' + index)
      },
      swiperChange(e) {
        const index = e.detail.current;
        this.tabsCurrent = index;
        this.initSwiperHeight('.list' + index)
      }
    },
  }
</script>

```

