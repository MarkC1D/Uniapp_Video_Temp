<template>
	<view class="container">
		<swiper :vertical="true" class="swiper" :circular="true" :display-multiple-items="1" @change="changeVideoStatus">
			<swiper-item v-for="item in videoList" :key="item.id" class="swiper-item">
				<!-- 				<cover-view>
					<video :src="item.src" class="videos" autoplay muted :controls="false" :show-center-play-btn="false" :enable-progress-gesture="false" loop></video>
				</cover-view> -->
				<view class="swipe-infomation">
					<view class="text">@{{ item.createby }}</view>
					<view class="text" style="font-size: 18px">{{ item.title }}</view>
					<view class="text" style="font-size: 15px">{{ item.desc }}</view>
					<uv-icon
						:name="muted ? 'volume-off-fill' : 'volume-fill'"
						color="white"
						size="30"
						style="float: right; padding: 0px 35px 0px 0px"
						@click="muted = !muted"
					></uv-icon>
				</view>
				<view class="video-status" @click="touchVideo(item.id)">
					<uv-icon v-if="videoStatus.playing === false" name="play-right-fill" :size="100" color="white"></uv-icon>
				</view>
				<DomVideoPlayer
					:ref="'videoPlayer' + item.id"
					:src="item.src"
					:muted="muted"
					@video-click="touchVideo(item.id)"
					loop
					style="height: 100%; background-color: black; z-index: -1"
				/>
			</swiper-item>
		</swiper>
	</view>
</template>

<script>
import DomVideoPlayer from 'uniapp-video-player';

export default {
	components: {
		DomVideoPlayer
	},
	data() {
		return {
			muted: true,
			videoList: [
				{ id: 1, src: 'http://api.yujn.cn/api/zzxjj.php', title: '这是一个测试标题', desc: '这是一条测试描述内容', createby: '测试用户' },
				{ id: 2, src: 'http://api.yujn.cn/api/zzxjj.php', title: '这是一个测试标题', desc: '这是一条测试描述内容', createby: '测试用户' },
				{ id: 3, src: 'http://api.yujn.cn/api/zzxjj.php', title: '这是一个测试标题', desc: '这是一条测试描述内容', createby: '测试用户' },
				{ id: 4, src: 'http://api.yujn.cn/api/zzxjj.php', title: '这是一个测试标题', desc: '这是一条测试描述内容', createby: '测试用户' },
				{ id: 5, src: 'http://api.yujn.cn/api/zzxjj.php', title: '这是一个测试标题', desc: '这是一条测试描述内容', createby: '测试用户' },
				{ id: 6, src: 'http://api.yujn.cn/api/zzxjj.php', title: '这是一个测试标题', desc: '这是一条测试描述内容', createby: '测试用户' },
				{ id: 7, src: 'http://api.yujn.cn/api/zzxjj.php', title: '这是一个测试标题', desc: '这是一条测试描述内容', createby: '测试用户' },
				{ id: 8, src: 'http://api.yujn.cn/api/zzxjj.php', title: '这是一个测试标题', desc: '这是一条测试描述内容', createby: '测试用户' },
				{ id: 9, src: 'http://api.yujn.cn/api/zzxjj.php', title: '这是一个测试标题', desc: '这是一条测试描述内容', createby: '测试用户' }
			],
			videoStatus: {}, // 存储每个视频的播放状态
			lastVideoIndex: 0,
			currentVideoIndex: 0
		};
	},
	methods: {
		changeVideoStatus(e) {
			const currentIndex = e.detail.current; // 当前 swiper 索引
			const lastVideoIndex = this.currentVideoIndex; // 上一个索引
			this.lastVideoIndex = this.currentVideoIndex; // 更新上一个索引
			this.currentVideoIndex = currentIndex; // 更新当前索引
			const currentVideoId = this.videoList[currentIndex].id; // 当前视频的 id
			const videoPlayer = this.$refs[`videoPlayer${currentVideoId}`] && this.$refs[`videoPlayer${currentVideoId}`][0];

			// 更新当前视频状态
			if (videoPlayer) {
				console.log(`视频 ${currentVideoId} 初始状态:`);
				console.log(`是否正在播放: ${videoPlayer.playing}`);
				console.log(`当前播放时间: ${videoPlayer.currentTime}`);
				console.log(`视频总时长: ${videoPlayer.duration}`);
				// 仅当视频未播放时调用 play()
				if (!videoPlayer.playing) {
					videoPlayer.play();
					console.log(`视频 ${currentVideoId} 已触发播放`);
					videoPlayer.playing = true;
					console.log(`是否正在播放: ${videoPlayer.playing}`);
					console.log(`当前播放时间: ${videoPlayer.currentTime}`);
					console.log(`视频总时长: ${videoPlayer.duration}`);
					this.videoStatus = videoPlayer;
				} else {
					// 如果已播放，直接更新状态
					this.$set(this.videoStatus, currentVideoId, {
						playing: videoPlayer.playing,
						currentTime: videoPlayer.currentTime,
						duration: videoPlayer.duration
					});
				}
			} else {
				console.error(`未找到 videoPlayer${currentVideoId} 实例`);
			}

			// 释放前一个视频资源
			if (lastVideoIndex !== currentIndex) {
				const prevVideoId = this.videoList[lastVideoIndex].id; // 前一个视频的 id
				const prevVideoPlayer = this.$refs[`videoPlayer${prevVideoId}`] && this.$refs[`videoPlayer${prevVideoId}`][0];
				if (prevVideoPlayer && prevVideoPlayer.playing) {
					prevVideoPlayer.pause(); // 或 prevVideoPlayer.remove()
					console.log(`前一个视频 ${prevVideoId} 已暂停播放`);
				} else {
					console.log(`前一个视频 ${prevVideoId} 未播放或未找到，无需处理`);
				}
			} else {
				console.log(`索引未变化，无前一个视频需要处理`);
			}

			// 判断滑动方向（用于调试）
			if (lastVideoIndex !== currentIndex) {
				if (currentIndex > lastVideoIndex || (currentIndex === 0 && lastVideoIndex === this.videoList.length - 1)) {
					console.log(`用户上滑：从索引 ${lastVideoIndex} 到 ${currentIndex}`);
				} else {
					console.log(`用户下滑：从索引 ${lastVideoIndex} 到 ${currentIndex}`);
				}
			}
		},
		touchVideo(id) {
			const videoPlayer = this.$refs[`videoPlayer${id}`][0];
			if (videoPlayer) {
				console.log(`Video ${id} 状态:`);
				console.log(`是否正在播放: ${videoPlayer.playing}`);
				console.log(`当前播放时间: ${videoPlayer.currentTime}`);
				console.log(`视频总时长: ${videoPlayer.duration}`);
				if (videoPlayer.playing === false) {
					console.log('视频被点击，当前视频未在播放，开始播放');
					this.videoStatus.playing = true;
					videoPlayer.play();
				} else if (videoPlayer.playing === true) {
					console.log('视频被点击，当前视频正在播放，停止播放');
					this.videoStatus.playing = false;
					videoPlayer.pause();
				}
			} else {
				console.log(`未找到 videoPlayer${id} 实例`);
			}
		},
		parse() {
			const videoPlayer = this.$refs.domVideoPlayer;
			this.videoStatus.playing = true;
			videoPlayer.parse();
		},
		play(id) {
			console.log('play触发');
			const videoPlayer = this.$refs[`videoPlayer${id}`][0];
			this.videoStatus.playing = false;
			videoPlayer.play();
		}
	},
	onReady() {
		if (this.muted === true) {
			uni.showToast({
				title: '当前默认已开启静音，如需播放声音请点击右下方音量按钮',
				icon: 'none'
			});
		}
		const firstVideoId = this.videoList[0].id;
		const videoPlayer = this.$refs[`videoPlayer${firstVideoId}`] && this.$refs[`videoPlayer${firstVideoId}`][0];
		this.videoStatus = videoPlayer;
		console.log(`第一个视频 (ID: ${firstVideoId}) 数据:`);
		console.log(`是否正在播放: ${this.videoStatus.playing}`);
		console.log(`当前播放时间: ${videoPlayer.currentTime}`);
		console.log(`视频总时长: ${videoPlayer.duration}`);
		// 开始播放视频
		setTimeout(() => {
			videoPlayer.play();
		}, 1000);
	}
};
</script>

<style scoped>
.container {
	width: 100%;
	height: 100vh;
	overflow: hidden;
}
.swiper {
	width: 100%;
	height: 100vh;
}
.swiper-item {
	width: 100%;
	height: 100%;
	position: relative;
}
.videos {
	width: 100%;
	height: 100%;
	object-fit: cover;
}

.text {
	color: white;
	font-size: 20px;
}

.swipe-infomation {
	position: absolute;
	bottom: 0;
	left: 0;
	width: 100%;
	padding: 20px;
	z-index: 1;
}

.video-status {
	position: absolute;
	width: 100%;
	height: 100%;
	display: flex;
	justify-content: center;
}
</style>
