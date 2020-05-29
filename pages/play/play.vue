<template>
	<view class="content">
		<view class="status-bar"></view>
		<!-- header -->
		<view class="player-header">
			<text class="player-header-icon text-white cuIcon-back" @tap="goBack"></text>
			<view class="player-header-title">
				<view class="title-name">
					{{songList[nowIndex].name}}
				</view>
				<view class="title-msg">
					{{songList[nowIndex].author}}
				</view>
				
			</view>
			<text class="player-header-share text-white cuIcon-share"></text>
		</view>
		<!-- videodisc -->
		<view class="player-videodisc">
			<image src="../../static/images/videodisc.png" mode=""></image>
		</view>
		<!-- optBar -->
		<view class="play-opt-bar">
			<view class="" v-for="(item, index) in optList" :key="index">
				<text class="text-white" :class="item.icon"></text>
			</view>

		</view>
		<!-- slider -->
		<view class="player-slider">
			<view class="player-currentTime">
				{{nowtime}}
			</view>
			<slider 
			class="slider"
			min="0"
			:max="duration"
			:value="nowtime"
			activeColor="#dedede" 
			backgroundColor="#b6b6b6"
			block-size="12"
			@change="changeProgress"
			/>
			<view class="player-duration">
				{{alltime}}
			</view>
		</view>
		<!-- playbar -->
		<view class="play-bar">
			<!-- 播放方式 -->
			<view class="">
				<view  @tap="updateWay()" v-if="playWay==2">
					<image  class="iconbtn" src="../../static/images/icon/suijibofang.png"/>
				</view>
				<view  @tap="updateWay()" v-if="playWay==1">
					<image  class="iconbtn" src="../../static/images/icon/xunhuanbofang.png"/>
				</view>
				<view  @tap="updateWay()" v-if="playWay==0">
						<image  class="iconbtn" src="../../static/images/icon/danquxunhuan.png"/>
				</view>
			</view>
			<view class="">
				<text class="text-white cuIcon-backwardfill"  @tap="last()"></text>
			</view>
			
			<view class="play-menu" v-if="playState===1">
				<text class="text-white cuIcon-playfill"  @tap="play()"></text>
			</view>
			<view  class="play-menu" v-else>
				<text class="text-white cuIcon-stop" @tap="pause()"></text>
			</view>
			
			<view class="">
				<text class="text-white cuIcon-play_forward_fill"  @tap="next()"></text>
			</view>
			<view class="">
				<text class="text-white cuIcon-list" ></text>
			</view>
			
		</view>
	</view>
</template>

<script>
	let bgAudioMannager = ''
	export default {
		data() {
			return {
				optList:[{
						'id': 0,
						'icon': 'cuIcon-like'
					},{
						'id': 1,
						'icon': 'cuIcon-down'
					},{
						'id': 2,
						'icon': 'cuIcon-notice'
					},{
						'id': 3,
						'icon': 'cuIcon-message'
					},{
						'id': 4,
						'icon': 'cuIcon-moreandroid'
					},
				],
				duration: '100',
				ifPlay: false,
				
				songList: [{
						"name": "稻香",
						"author": "周杰伦",
						"src": "https://wfd-pic.oss-cn-shenzhen.aliyuncs.com/audio/202005201535525ec4ddd80562c.flac",
						"img": "http://p2.music.126.net/_UOTSqLC8qHRivyuUBC9OQ==/18200215974944920.jpg"				
					},
					{
						"name": "七里香",
						"author": "周杰伦",
						"src": "https://wfd-pic.oss-cn-shenzhen.aliyuncs.com/audio/202005212133355ec6832f91b32.flac",
						"img": "http://p2.music.126.net/D9_qDt18yiHxVPr6CRGgLA==/109951163406952902.jpg"
				
					},
					{
						"name": "游园会",
						"author": "周杰伦",
						"src": "https://wfd-pic.oss-cn-shenzhen.aliyuncs.com/audio/202005212139325ec68494d9417.flac",
						"img": "http://p2.music.126.net/_UOTSqLC8qHRivyuUBC9OQ==/18200215974944920.jpg"
					},
					{
						"name": "止战之殇",
						"author": "周杰伦",
						"src": "https://wfd-pic.oss-cn-shenzhen.aliyuncs.com/audio/202005212153185ec687ce5a9ba.flac",
						"img": "http://p2.music.126.net/_UOTSqLC8qHRivyuUBC9OQ==/18200215974944920.jpg"
					},
					
				],
				indicatorDots: true,
				autoplay: true,
				interval: 2000,
				playWay: 1,
				playState: 1,
				nowIndex: 0,
				currentTime: 0,
			}
		},
		onLoad() {
			bgAudioMannager = uni.getBackgroundAudioManager();
			//如果要默认播放的话，把以下注释取消
			//this.bgAudioInnit();
		},
		computed: {
			'nowtime': function() {
				var that = this
				var s = that.currentTime
				//计算分钟
				//算法：将秒数除以60，然后下舍入，既得到分钟数
				var h;
				h = Math.floor(s / 60);
				//计算秒
				//算法：取得秒%60的余数，既得到秒数
				s = s % 60;
				//将变量转换为字符串
				h += '';
				s += '';
				//如果只有一位数，前面增加一个0
				h = (h.length == 1) ? '0' + h : h;
				s = (s.length == 1) ? '0' + s : s;
				return h + ':' + s;
			},
			'alltime': function() {
				var that = this
				var s = that.duration
				//计算分钟
				//算法：将秒数除以60，然后下舍入，既得到分钟数
				var h;
				h = Math.floor(s / 60);
				//计算秒
				//算法：取得秒%60的余数，既得到秒数
				s = s % 60;
				//将变量转换为字符串
				h += '';
				s += '';
				//如果只有一位数，前面增加一个0
				h = (h.length == 1) ? '0' + h : h;
				s = (s.length == 1) ? '0' + s : s;
				return h + ':' + s;
			}		
		},
		methods: {
			sliderChange(e) {
				this.currentTime = e.detail.value
				bgAudioMannager.seek(this.currentTime)
			},
			bgAudioInnit() {
				var that = this
				console.log(that.songList[that.nowIndex]);
				bgAudioMannager.title = that.songList[that.nowIndex].name;
				bgAudioMannager.singer = that.songList[that.nowIndex].author;
				bgAudioMannager.coverImgUrl = that.songList[that.nowIndex].img;
				bgAudioMannager.src = that.songList[that.nowIndex].src;
			
				bgAudioMannager.onPlay(() => {
					that.playFunc()
				})
				bgAudioMannager.onPause(() => {
					that.pauseFunc()
				})
				bgAudioMannager.onEnded(() => {
					that.next()
				})
				bgAudioMannager.onTimeUpdate(() => {
					this.currentTime = Math.floor(bgAudioMannager.currentTime);
				})
				bgAudioMannager.onPrev(function() {
					that.last()
				})
				bgAudioMannager.onNext(function() {
					that.next()
				})
			
				bgAudioMannager.onError(function() {
					that.error()
				})
				bgAudioMannager.onWaiting(function() {
					that.playState = 0
				})
			
				bgAudioMannager.onCanplay(function() {
					that.duration = Math.floor(bgAudioMannager.duration)
				})
			
			},
			error() {
				this.playState = 0
			},
			play() {
				var that = this
				if (bgAudioMannager.src == undefined) {
					this.bgAudioInnit()
				}
				bgAudioMannager.play()
			
			},
			pause() {
				var that = this
			
				bgAudioMannager.pause()
			},
			playFunc() {
				var that = this
				that.playState = 0
			},
			pauseFunc() {
				var that = this
				that.playState = 1
			},
			last() {
				var that = this
				//随机
				if (that.playWay == 2) {
					that.nowIndex = Math.floor(Math.random() * this.songList.length);
				} else if (that.playWay == 1) { //顺序播放
					//顺序
					if (that.nowIndex < 1) {
						that.nowIndex = that.songList.length - 1
					} else {
						that.nowIndex = that.nowIndex - 1
					}
				} else { //单曲播放 不做处理
			
				}
			
				that.bgAudioInnit()
			},
			next() {
				var that = this
				//随机
				if (that.playWay == 2) {
					that.nowIndex = Math.floor(Math.random() * this.songList.length);
				} else if (that.playWay == 1) { //顺序播放
					//顺序
					if (that.nowIndex >= (this.songList.length - 1)) {
						that.nowIndex = 0
					} else {
						that.nowIndex = that.nowIndex + 1
					}
				} else { //单曲播放 不做处理
			
				}
				that.bgAudioInnit()
			},
			updateWay() {
				var that = this
				if (that.playWay == 2) {
					that.playWay = 0
				} else {
					that.playWay = that.playWay + 1
				}
			
			},
			changeProgress(e){
				bgAudioMannager.seek(e.detail.value);
				// this.nowtime = e.detail.value
				this.play();
			},
		
		}
	}
</script>

<style>
	@import url("./index.css");
</style>
