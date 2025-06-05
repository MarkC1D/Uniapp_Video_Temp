# Uniapp_Video_Temp
Uniapp Vue3版本仿抖音短视频短视频实现模板

使用库 [uniapp-video-player](https://github.com/liusheng22/uniapp-video-player) 以及Uniapp自带Swiper为基础实现短视频 无其他插件

实测比较原生Video标签性能更好 因为Video标签有层级过高的问题(已经查阅过文档可以使用cover-view解决，但是我尝试不行，依旧存在卡顿)

仅在Uniapp Vue3版本 Android App经过测试没问题 其他可以自行测试

实现功能：
* 上下滑动切换短视频(基于Uniapp自带组件Swiper)
* 短视频播放(基于开源视频播放组件 uniapp-video-player)
* 上下切换暂停上一个视频播放(基于开源视频播放组件 uniapp-video-player)

效果：

![image](https://github.com/user-attachments/assets/cf11d050-6fe4-4ff6-b2ef-42d2d0c511a8)

需要可以自取 有Bug就直接修吧[手动笑哭]因为不是很复杂 只是一个Demo
