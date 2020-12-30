<template>
	<view>
		<view class="cu-custom">
			<cu-custom :isBack="true">
				<block slot="backText">返回</block>
			</cu-custom>
		</view>
		<view class="buju">
			<l-mask :show="true" :center="true" opacity="0.5">
				<view class='mask-content'>
					<view class='mask-close'>
						<l-transition :show="true" name="fade-right" duration="1000">
							<view>
								<l-input label="学号" @lininput="xueHao" type="number" maxlength=12 placeholder="请输入学号" :required="true" :clear="true"
								 @linclear="xuehaoClear" />
							</view>
							<view class="password">
								<l-input label="密码" type="password" @lininput="passWord" placeholder="请输入密码" :required="true" :clear="true"
								 @linclear="passWordClear" />
							</view>
							<view class="padding">
								<button v-if="xuehao&&password" class="cu-btn block bg-gradual-green shadow margin-tb-sm lg" @getuserinfo="login"
								 open-type="getUserInfo">登录</button>
								<button class="cu-btn block bg-gradual-green shadow margin-tb-sm lg" v-else @click="pxNull">登录</button>
							</view>
						</l-transition>
					</view>
				</view>
			</l-mask>
			<l-message />
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				xuehao: "",
				password: "",
				avatarUrl: '',
				nickName: '',
				openid: ''
			};
		},
		methods: {
			// 拿到输入的学号
			xueHao(e) {
				this.xuehao = e.target.value.replace(/(^\s*)|(\s*$)/g, "")
			},
			// 拿到输入的密码
			passWord(e) {
				this.password = e.target.value.replace(/(^\s*)|(\s*$)/g, "")
			},
			// 清除学号
			xuehaoClear() {
				this.xuehao = ''
			},
			// 清除密码
			passWordClear() {
				this.password = ''
			},
			// 学号或密码错误时提示的信息
			wxShowMessage(type, icon, content) {
				wx.lin.showMessage({
					type: type,
					icon: icon,
					content: content
				})
			},
			// 当用户没有输入学号或者密码时，或者都没有输入时
			pxNull() {
				if (!this.xuehao && this.password) {
					this.wxShowMessage('error', 'error', '学号不能为空')
				} else if (!this.password && this.xuehao) {
					this.wxShowMessage('error', 'error', '密码不能为空')
				} else if (!this.xuehao && !this.password) {
					this.wxShowMessage('error', 'error', '学号和密码都不能为空')
				}
			},
			// 缓存
			setStorage(data) {
				uni.setStorage({
					key: 'openid',
					data: data,
					success(res) {
						// console.log("缓存成功")
					}
				})
			},
			 login(e) {
				const that = this
				if (e.detail.rawData != null) {
					that.avatarUrl = e.detail.userInfo.avatarUrl
					that.nickName = e.detail.userInfo.nickName
					// console.log(e)
					 uni.login({
						success(data) {
							uni.showLoading({
								title: '登录中...',
								mask: true
							});
							uniCloud.callFunction({
								name: 'getOPenId',
								data: {
									code: data.code
								}
							}).then(res => {
								// console.log(res.result.data.openid)
								that.openid = res.result.data.openid
								// 判断数据库是否存在该openid
								that.judgeOpenidExist(res.result.data.openid)

							})
						}
					})
				} else {
					that.wxShowMessage('error', 'error', '微信绑定失败')
				}
			},
			// 判断数据库是否存在该openid
			judgeOpenidExist(data) {
				const that = this
				uniCloud.callFunction({
					name: 'judgeOpenidExist',
					data: {
						openid: data
					}
				}).then(res => {
					// console.log("openid",res.result.data.length)
					if (res.result.data.length == 0) {
						// 不存在openid
						// 判断数据库中是否已经存在该学号
						that.judgeXuehaoExist()
					} else {
						// 存在openid
						uniCloud.callFunction({
							name: 'getUserInfoByOpenid',
							data: {
								openid: data,
							}
						}).then(res => {
							// console.log(res.result.data,that.xuehao)
							if (res.result.data[0].sno != that.xuehao) {
								uni.hideLoading()
								that.wxShowMessage('error', 'error', '学号不一致，该微信号已经绑定过了')
							} else {
								uniCloud.callFunction({
									name: 'login',
									data: {
										xuehao: that.xuehao,
										password: that.password
									}
								}).then(res => {
									console.log(res.result)
									if (res.result.data.data == '密码错误') {
										uni.hideLoading()
										that.wxShowMessage('error', 'error', '密码错误')
									} else {
										uni.hideLoading()
										that.wxShowMessage('success', 'success', '登陆成功')
										uni.switchTab({
											url: '../home/home'
										})
										setTimeout(function() {
											uni.$emit('userInfo', {
												data: { ...res.result.data.data,
													'avatarUrl': that.avatarUrl
												}
											})
										}, 500)
										that.xuehao = ""
										that.password = ""
										// 将获取到的openid存到缓存中
										that.setStorage(that.openid)
									}
								}).catch(error => {
									that.wxShowMessage('error', 'error', '请求超时')
									uni.hideLoading()
								})
							}
						})
					}
				})
			},
			// 判断数据库中是否已经存在该学号
			judgeXuehaoExist(data) {
				const that = this
				uniCloud.callFunction({
					name: 'judgeXuehaoExist',
					data: {
						xuehao: that.xuehao
					}
				}).then(res => {
					// console.log(res)
					if (res.result.data.length == 0) {
						// 不存在学号
						// 登陆，将登陆信息存入数据库
						uniCloud.callFunction({
							name: 'login',
							data: {
								xuehao: that.xuehao,
								password: that.password
							}
						}).then(res => {
							// console.log(res.result.data.data)
							if (res.result.data.data == '密码错误') {
								uni.hideLoading()
								that.wxShowMessage('error', 'error', '密码错误')
							} else if (res.result.data.data == '账户不存在') {
								uni.hideLoading()
								that.wxShowMessage('error', 'error', '账户不存在')
							} else {
								// console.log(res.result)
								uni.hideLoading()
								that.wxShowMessage('success', 'success', '登陆成功')
								uni.switchTab({
									url: '../home/home'
								})
								setTimeout(function() {
									uni.$emit('userInfo', {
										data: { ...res.result.data.data,
											'avatarUrl': that.avatarUrl
										}
									})
								}, 500)
								that.xuehao = ""
								that.password = ""
								// 将获取到的openid存到缓存中
								that.setStorage(that.openid)
								uniCloud.callFunction({
									name: 'addUserInfo',
									data: {
										...res.result.data.data,
										openid: that.openid,
										avatarUrl: that.avatarUrl,
										nickName: that.nickName
									}
								})
							}
						}).catch(error => {
							that.wxShowMessage('error', 'error', '请求超时')
							uni.hideLoading()
						})
					} else {
						// 存在学号，提示该学号已经存在
						that.wxShowMessage('warning', 'warning', '该学号已经存在')
					}
				})
			}
		}
	}
</script>

<style lang="scss">
	page {
		background-image: url("https://vkceyugu.cdn.bspapp.com/VKCEYUGU-aliyun-l5j2urmfatyb41ea37/458f9510-240b-11eb-8ff1-d5dcf8779628.jpg");
		background-repeat: no-repeat;
		background-size: 100% 100%;
		-moz-background-size: 100% 100%;
	}

	.buju {
		margin-top: 460rpx;

		cu-custom {
			z-index: 100;
		}

		.password {
			margin-top: 52rpx;
			margin-bottom: 32rpx;
		}

		input,
		label {
			color: #FFFFFF;
		}
	}
</style>
