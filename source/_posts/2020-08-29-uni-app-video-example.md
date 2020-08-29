---
title: uni-app 的 video 组件的使用
date: 2020-08-29 23:03:08
tags:
  - uni-app
  - video
categorites:
  - [知识总结, uni-app]
excerpt: uni-app 的 video 组件的使用, 兼容微信H5及微信小程序端
---

**相关说明**

1. `id="videoWrap"`: 用于创建并返回 video 上下文 videoContext 对象;
2. `src`: 要播放视频的资源地址;
3. `controls`: 是否显示默认播放控件（播放/暂停按钮、播放进度、时间）;
4. `show-center-play-btn`: 是否显示视频中间的播放按钮;
5. `object-fit`: 当视频大小与 video 容器大小不一致时，视频的表现形式。contain：包含，fill：填充，cover：覆盖;
6. `poster`: 视频封面的图片网络资源地址，如果 controls 属性值为 false, 则设置 poster 无效(官网说明, 实际上我测试是有效的?);
7. `:custom-cache="false"`: 解决 video 组件播放会卡顿、绿屏,尤其是在快进、拖动时间条时;
8. `show-mute-btn`: 是否显示静音按钮; - 微信小程序;
9. `muted`: 是否静音播放; **需要自定义按钮操作静音时, 必须在组件写上 muted 属性**;
10. `x5-video-player-type="h5"`: 防止微信内置浏览器强制调用原生播放器播放; 如果没有设置这个, `:controls="false"` 不起作用;
11. `@ended`: 当播放到末尾时触发 ended 事件;
12. `@error`: 视频播放出错时触发;

**代码示例**

```html
<!-- video 组件使用示例 -->
<video
  id="videoWrap"
  class="video-wrap"
  :src="videoData.videoUrl"
  :controls="false"
  :show-center-play-btn="false"
  object-fit="contain"
  :poster="videoData.imageUrl"
  :custom-cache="false"
  show-mute-btn
  :muted="isMuted"
  x5-video-player-type="h5"
  @ended="handleEnded"
  @error="videoErrorCallback"
></video>

<!-- 控制按钮 - 播放 -->
<view v-if="!isPlaying" @tap="handlePlay"></view>
<!-- 控制按钮 - 暂停 -->
<view v-else @tap="handlePause"></view>

<!-- 音量开关 - 取消静音 -->
<view v-if="isMuted" @tap="setIsMuted(false)"></view>
<!-- 音量开关 - 静音 -->
<view v-else @tap="setIsMuted(true)"></view>
```

```js
export default {
  data() {
    return {
      // 正在播放
      isPlaying: false,
      // 静音
      isMuted: false,
    };
  },

  mounted() {
    // 更多请查找官方文档 https://uniapp.dcloud.io/api/media/video-context
    // 创建并返回 video 上下文 videoContext 对象。在自定义组件下，第二个参数传入组件实例this，以操作组件内 <video> 组件。
    this.videoContext = uni.createVideoContext("videoWrap", this);
  },

  // 方法
  methods: {
    // 播放视频
    handlePlay() {
      this.isPlaying = true;
      // 调用 play() 必须使用 uni.createVideoContext 创建 video
      this.videoContext.play();
    },

    // 暂停视频
    handlePause() {
      this.isPlaying = false;
      // 调用 pause() 必须使用 uni.createVideoContext 创建 video
      this.videoContext.pause();
    },

    // 设置静音
    setIsMuted(val) {
      this.isMuted = val;
      // 注意: 必须在组件写上 muted 属性
      this.videoContext.muted = val;
    },

    // 播放结束
    handleEnded() {
      this.isPlaying = false;
    },

    // 播放错误
    videoErrorCallback(e) {
      uni.showToast({
        title: `视频播放错误信息:${e.target.errMsg}`,
        duration: 2000,
        icon: "none",
      });
    },
  },
};
```

**参考**
[video - uni-app 官网](https://uniapp.dcloud.io/component/video)
[视频组件控制 API - uni-app 官网](https://uniapp.dcloud.io/api/media/video-context)
