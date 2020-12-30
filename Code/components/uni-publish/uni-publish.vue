<template>
	<view>

		<form>
			<view class="cu-form-group">
				<view class="title">商品标题</view>
				<input placeholder="请输入商品标题" name="input" v-model="title"></input>
			</view>

			<view class="cu-form-group">
				<view class="title">商品类型</view>
				<picker @change="PickerChange" :value="index" :range="picker">
					<view class="picker" v-model="classify">
						{{index>-1?picker[index]:'请选择商品类型'}}
					</view>
				</picker>
			</view>

			<view class="cu-form-group">
				<view class="title">免费赠送</view>
				<switch @change="switchChange" :class="isFree?'checked':''" :checked="isFree?true:false"></switch>
			</view>

			<view class="cu-form-group">
				<view class="title">商品售价</view>
				<input placeholder="请输入商品售价" @input="priceInput" name="input" type="number" v-model="price" :disabled="isFree"
				 :style="{color:price===0?'#c6c6c6':''}"></input>
			</view>

			<view class="cu-form-group">
				<view class="title">联系人</view>
				<input placeholder="请输入联系人姓名" name="input" v-model="connection"></input>
			</view>

			<view class="cu-form-group">
				<view class="title">手机号码</view>
				<input placeholder="请输入手机号码" maxlength=11 name="input" type="number" v-model="phone"></input>
				<view class="cu-capsule radius">
					<view class='cu-tag bg-gradual-orange '>
						+86
					</view>
					<view class="cu-tag line-orange">
						中国大陆
					</view>
				</view>
			</view>


			<view class="cu-form-group">
				<view class="title">联系人微信</view>
				<input placeholder="请输入微信" name="input" v-model="weixin"></input>
			</view>

			<view class="cu-bar bg-white">
				<view class="action">
					图片上传（至少一张）
				</view>
				<view class="action">
					{{imgList.length}}/4
				</view>
			</view>
			<view class="cu-form-group">
				<view class="grid col-4 grid-square flex-sub">
					<view class="bg-img" v-for="(item,index) in imgList" :key="index" @tap="ViewImage" :data-url="imgList[index]">
						<image :src="imgList[index]" mode="aspectFill"></image>
						<view class="cu-tag bg-red" @tap.stop="DelImg" :data-index="index">
							<text class='cuIcon-close'></text>
						</view>
					</view>
					<view class="solids" @tap="ChooseImage" v-if="imgList.length<4">
						<text class='cuIcon-cameraadd'></text>
					</view>
				</view>
			</view>



			<view class="cu-form-group align-start">
				<view class="title">描述</view>
				<textarea placeholder="详细描述一下商品" v-model="description"></textarea>
			</view>


			<view class="padding flex flex-direction">
				<button open-type="getUserInfo" class="cu-btn bg-gradual-orange lg" :disabled="isDisabled" @click="publish">发布</button>
			</view>
		</form>
		<l-message />
		<l-dialog />
	</view>
</template>
<script>
	export default {
		data() {
			return {
				// noPublish:true,
				isDisabled:false,
				openid: '',
				nickName: '',
				avatarUrl: '',
				title: '',
				price: null,
				classify: '',
				isFree: false,
				connection: '',
				phone: '',
				weixin: '',
				description: '',
				imgList: [],
				ctime: '',
				utime: '',
				uploadImg: [],
				index: -1,
				picker: ['其他', '生活用品', '体育用品', '学习用品', '电子数码', '服饰'],
				count: 4
			};
		},
		methods: {
			// 价格输入
			priceInput() {
				if (this.price == 0) {
					this.isFree = true
				}
			},
			// 填写信息错误提示
			wxShowMessage(type, icon, content) {
				wx.lin.showMessage({
					type: type,
					icon: icon,
					content: content,
					success() {
						uni.$emit("changeNoPublish", true)
					}
				})
			},
			//发布
			publish() {
				const that = this;
				that.isDisabled = true
				if (!this.title || !this.classify || !this.connection || !this.phone || !this.weixin || !this.description || (this.price ==
						null && this.isFree == false)) {
					uni.$emit("changeNoPublish", false)
					this.wxShowMessage("error", "error", "填写信息不完整")
					that.isDisabled = false
				} else if (this.phone.length !== 11) {
					// console.log("请填写正确的手机号码")
					uni.$emit("changeNoPublish", false)
					this.wxShowMessage("error", "error", "请填写正确的手机号码")
					that.isDisabled = false
				} else if (this.imgList.length == 0) {
					uni.$emit("changeNoPublish", false)
					this.wxShowMessage("error", "error", "至少选择一张图片")
					that.isDisabled = false
				} else if (this.price < 0) {
					uni.$emit("changeNoPublish", false)
					this.wxShowMessage("error", "error", "请输入正确的价格")
					that.isDisabled = false
				} else {
					// 1.缓存拿openid，用户的头像，名字，
					uni.getStorage({
						key: 'openid',
						success(openidRes) {
							// console.log("openidRes",openidRes.data)
							that.openid = openidRes.data
							// 2.获取用户信息
							uni.getUserInfo({
								success(userInfoRes) {
									// console.log("userInfoRes",userInfoRes)
									that.nickName = userInfoRes.userInfo.nickName;
									that.avatarUrl = userInfoRes.userInfo.avatarUrl
									that.publishMessage(that, openidRes.data)
								}
							})
						}
					})
				}
			},
			// 上传图片并发布
			async uploadImage(imageUrl, openidResdata, i) {
				// console.log("上传图片并发布")
				const that = this
				await uniCloud.uploadFile({
					cloudPath: Math.random() * 100 + ".png",
					filePath: imageUrl,
					success(uploadImgRes) {
						that.uploadImg.push(uploadImgRes.fileID) //图片的https路径
						if (i === that.imgList.length - 1) {
							// console.log("启动云函数")
							uniCloud.callFunction({
								name: 'addPublishMessage',
								data: {
									"openid": openidResdata,
									"avatarUrl": that.avatarUrl,
									"nickName": that.nickName,
									"title": that.title,
									"classify": that.classify,
									"isFree": that.isFree,
									"price": parseInt(that.price),
									"connection": that.connection,
									"phone": that.phone,
									"weixin": that.weixin,
									"description": that.description,
									"imgList": that.uploadImg,
									"ctime": new Date().getTime(),
									"utime": '',
									"views": 0,
									"commentsCount": 0,
									"status": '已上架'
								}
							}).then(res => {
								uni.hideLoading()
								uni.$emit("changeNoPublish", false)
								that.wxShowMessage('success', 'success', '发布成功')
								that.isDisabled = false
								that.title = ""
								that.index = -1
								that.picker[that.index] = "请选择商品类型"
								that.isFree = false
								that.connection = ""
								that.price = null
								that.phone = ""
								that.weixin = ""
								that.description = ""
								that.imgList = []
								// 得到用户发布商品的总数哦
								uniCloud.callFunction({
									name: 'getUserPublishStoreByOpenid',
									data: {
										openid: that.openid
									},
								}).then(res => {
									uni.$emit('changeStoreCount', res.result.data.length)
								})
							}).catch(res => {
								uni.hideLoading()
								that.wxShowMessage('error', 'error', '请求接口失败')
							})
						}
					}
				})
			},
			// 发布信息
			publishMessage(that, openidResdata) {
				const isThat = this
				wx.lin.showDialog({
					type: "confirm",
					content: "确定发布吗？",
					showTitle: false,
					success: (res) => {
						if (res.confirm) {
							uni.showLoading({
								title: '发布中...',
								mask: true
							})
							for (let i = 0; i < that.imgList.length; i++) {
								// 上传图片并发布
								that.uploadImage(that.imgList[i], openidResdata, i)
							}
						}else{
							isThat.isDisabled=false
						}
					}
				})
			},
			// 商品类型选择
			PickerChange(e) {
				this.index = e.detail.value
				this.classify = this.picker[this.index]
			},
			// 是否免费赠送选择
			switchChange(e) {
				// this.isFree = e.detail.value
				if (e.target.value === true) {
					this.isFree = !this.isFree;
					this.price = 0
				} else {
					this.price = null
					this.isFree = !this.isFree
				}
			},
			// 图片选择
			ChooseImage() {
				const that = this;
				uni.chooseImage({
					count: that.count, //默认9
					sizeType: ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都有
					success: (e) => {
						for (let i = 0; i < e.tempFilePaths.length; i++) {
							if (e.tempFiles[i].size <= 2097152) {
								that.imgList.push(e.tempFilePaths[i])
								that.count = that.count - 1;
							} else {
								uni.showToast({
									title: '图片不能大于2MB',
									duration: 1500,
									mask: true,
									icon: 'none'
								});
							}
						}

					}
				});
			},
			// 预览图片 
			ViewImage(e) {
				uni.previewImage({
					urls: this.imgList,
					current: e.currentTarget.dataset.url
				});
			},
			// 删除图片
			DelImg(e) {
				const that = this
				uni.showModal({
					title: '温馨提示',
					content: '确定要删除吗？',
					cancelText: '取消',
					confirmText: '确定',
					success: res => {
						if (res.confirm) {
							this.imgList.splice(e.currentTarget.dataset.index, 1)
							that.count += 1
						}
					}
				})
			}
		}
	}
</script>


<style>
	.cu-form-group {
		min-height: 80rpx;
	}

	.cu-form-group .title {
		min-width: calc(4em + 15px);
	}

	.cu-form-group>view:nth-of-type(1):before {
		content: '*';
		color: red;
	}

	.cu-bar>view.action:nth-of-type(1):before {
		content: '*';
		color: red;
	}

	.cu-form-group>view.grid-square:before {
		content: '';
	}

	.bg-white {
		border-top: 1px solid #eee;
	}
</style>
