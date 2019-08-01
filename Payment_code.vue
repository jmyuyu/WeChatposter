<template>
	<view class="content">	
		<canvas  style="width: 100%;height: 1050upx;opacity: 0;" canvas-id="myCanvas"/>
		</canvas>
		<view class="content"  style="position: fixed;z-index: 999;width: 100vw;">
			<view style="width: 100%;border-top: 2upx solid #EEEEEE;"></view>
			<view class="invicode-box">
				<view style="width:100%;background:#ffffff;">
				<view class='merchan'>{{ list.merchantName }}</view>	
				</view>
				<view class="box">收款码</view>
				<view class="code-pic">
					<view class="pic"><image :src="list.paymentCodeUrl" mode=""></image></view>
				</view>				
				<view class="code-title">
					<image class="icon_img" src="../../../static/img/hspics/752597618265658563.png">
				</image>&nbsp;&nbsp;乐花生&nbsp;&nbsp;扫码付款</view>
			</view>		
			<view class="Saveshare"><view class="save" @click="csss">保存收款码</view></view>
		</view>
		
	</view>
		

	
</template>

<script>
export default {
	data() {
		return {
			cs: '',
			list: {
				merchantName: '',
				paymentCodeUrl: ''
			},
			company: '杭州店址商务有限公司1',
			imgs: '../../../static/img/logo7.png', //今日收到宝贝数量
			quantity: '123' //今日收到宝贝数量
		};
	},
	created() {
		this.getcodes();
		this.getcode();
		let data = {
			merchantId: uni.getStorageSync('merchantId')
		};
		this.$store.dispatch('paymentCode', data).then(res => {
			this.list.merchantName = res.data.data.merchantName;
			console.log(res);
		});
	},
	methods: {
		csss() {
			let that = this;
			wx.downloadFile({
			url: that.list.paymentCodeUrl,
			success: function(res) {
				
			
			const ctx = uni.createCanvasContext('myCanvas');
			// begin path
			ctx.rect(0, 0, 750, 80);
			ctx.setFillStyle('#FFFFFF');
			ctx.fill();

			let list = that.list.merchantName;
			let arr = list.split('');
			let name = '';
			let name1 = '';
			ctx.font = '28px bold 黑体';
			// 设置颜色
			ctx.fillStyle = '#000';
			// 设置水平对齐方式
			ctx.textAlign = 'center';
			// 设置垂直对齐方式
			ctx.textBaseline = 'middle';
			// 绘制文字（参数：要写的字，x坐标，y坐标）
			if (arr.length < 12) {
				ctx.fillText(list, 200, 30);
			} else {
				for (let i = 0; i <= arr.length; i++) {
					if (i <= 11) {
						name += arr[i];
					}
				}
				ctx.fillText(name, 200, 30);
				let arrs = arr.splice(12, 1);
				ctx.fillText(arrs, 200, 60);
			}

			ctx.beginPath();
			ctx.rect(0, 80, 750, 500);
			ctx.setFillStyle('#ff8b3d');
			ctx.fill();
			ctx.font = '30px bold 黑体';
			ctx.fillStyle = '#fff';
			// 设置垂直对齐方式
			ctx.textBaseline = 'middle';
			// 绘制文字（参数：要写的字，x坐标，y坐标）
			ctx.fillText('付款码', 200, 130);

			ctx.drawImage(res.tempFilePath, 65, 155, 250, 250);
			ctx.drawImage('../../../static/img/hspics/752597618265658563.png', 70, 450, 50, 50);

			ctx.font = '18px bold 黑体';
			ctx.fillStyle = '#fff';
			// 设置垂直对齐方式
			ctx.textBaseline = 'middle';
			// 绘制文字（参数：要写的字，x坐标，y坐标）
			ctx.fillText('乐花生 扫码付款', 200, 480);

			ctx.draw();

			setTimeout(function() {
				let that = this;
				console.log(222);
				uni.canvasToTempFilePath({
					x: 0,
					y: 0,
					width: 750,
					height: 750,
					destWidth: 750,
					destHeight: 1050,
					canvasId: 'myCanvas',
					success: function(res) {
						console.log(res);
						that.cs = res.tempFilePath;

						var imgSrc = res.tempFilePath;
						// wx.downloadFile({
						// 	url: imgSrc,
						// 	success: function(res) {
								console.log(res);
								//图片保存到本地
								wx.saveImageToPhotosAlbum({
									filePath: res.tempFilePath,
									success: function(data) {
										wx.showToast({
											title: '保存成功',
											icon: 'success',
											duration: 2000
										});
									},
									fail: function(err) {
										console.log(err);
										if (err.errMsg === 'saveImageToPhotosAlbum:fail auth deny') {
											console.log('当初用户拒绝，再次发起授权');
											wx.openSetting({
												success(settingdata) {
													console.log(settingdata);
													if (settingdata.authSetting['scope.writePhotosAlbum']) {
														console.log('获取权限成功，给出再次点击图片保存到相册的提示。');
													} else {
														console.log('获取权限失败，给出不给权限就无法正常使用的提示');
													}
												}
											});
										}
									},
									complete(res) {
										console.log(res);
									}
								});
						
					}
				});
			}, 2000);
				}
			});
		},
		getcode() {
			wx.getSetting({
				success(res) {
					if (!res.authSetting['scope.writePhotosAlbum']) {
						wx.authorize({
							scope: 'scope.writePhotosAlbum',
							success() {
								console.log('授权成功');
							}
						});
					}
				}
			});
		},
		getcodes() {
			this.$store.dispatch('showMerchantPayQRCodes').then(res => {
				this.list.paymentCodeUrl = res.data.data;
			});
		},
		portal() {
			var imgSrc = this.list.paymentCodeUrl;
			wx.downloadFile({
				url: imgSrc,
				success: function(res) {
					console.log(res);
					//图片保存到本地
					wx.saveImageToPhotosAlbum({
						filePath: res.tempFilePath,
						success: function(data) {
							wx.showToast({
								title: '保存成功',
								icon: 'success',
								duration: 2000
							});
						},
						fail: function(err) {
							console.log(err);
							if (err.errMsg === 'saveImageToPhotosAlbum:fail auth deny') {
								console.log('当初用户拒绝，再次发起授权');
								wx.openSetting({
									success(settingdata) {
										console.log(settingdata);
										if (settingdata.authSetting['scope.writePhotosAlbum']) {
											console.log('获取权限成功，给出再次点击图片保存到相册的提示。');
										} else {
											console.log('获取权限失败，给出不给权限就无法正常使用的提示');
										}
									}
								});
							}
						},
						complete(res) {
							console.log(res);
						}
					});
				}
			});
		}
	}
};
</script>

<style lang="scss" scoped>
.merchan {
	padding-top: 15upx;
	margin-left: 10%;
	text-align: center;
	// width:559upx;
	min-height: 112upx;
	font-size: 48upx;
	font-family: PingFang-SC-Bold;
	font-weight: bold;
	color: rgba(0, 0, 0, 1);
	line-height: 66upx;
}
.title {
	width: 100%;
	height: 960upx;
	font-size: 46upx;
	text-align: center;
	line-height: 130upx;
	background-color: #fff;
	font-weight: bold;
}
.bottoms {
	width: 226upx;
	height: 31upx;
	position: absolute;
	left: 226upx;
	top: 66upx;
}
.content {
	.invicode-box {
		width: 100%;
		height: 100vh;
		background: #ff8b3d;
		// position: absolute;
		// top: 120upx;
		// left: 0;
		// right: 0;
		// margin: 0 auto;
		.box {
			font-size: 62upx;
			color: #fff;
			text-align: center;
			margin-top: 100upx;
		}
		.code {
			margin-top: 100upx;
			width: 100%;
			height: 135upx;
			line-height: 250upx;
			font-size: 28upx;
			font-family: PingFang-SC-Medium;
			font-weight: bold;
			color: rgba(51, 51, 51, 1);
			text-align: center;
			display: block;
		}
		.code-pic {
			width: 100%;
			height: 316upx;
			margin-top: 61upx;
			.pic {
				width: 457upx;
				height: 457upx;
				margin: 0 auto;
				image {
					width: 100%;
					height: 100%;
				}
			}
		}
		.code-title {
			width: 100%;
			font-size: 40upx;
			color: #fff;
			font-family: PingFang-SC-Medium;
			font-weight: 500;
			text-align: center;
			margin-top: 200upx;
			.icon_img {
				width: 70upx;
				height: 70upx;
				margin-right: 30upx;
				margin-bottom: -20upx;
			}
		}
	}

	.Saveshare {
		position: absolute;
		bottom: 0;
		left: 0;
		width: 100%;
		height: 108upx;
		border: 2upx solid #FFFFFF;
		background: #fff;
		.save {
			position: absolute;
			width: 300upx;
			height: 80upx;
			left: 220upx;
			bottom: 20upx;
			background: #ff6600;
			font-size: 36upx;
			line-height: 80upx;
			color: #fff;
			text-align: center;
			border-radius: 10upx;
		}
	}
}
</style>
