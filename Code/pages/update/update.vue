<template>
	<view>
		<view class="cu-custom " :style="{zIndex:(noUpdate?'100000':'-1')}">
			<cu-custom :isBack="true" bgColor="bg-gradual-orange">
				<block slot="content">修改</block>
				<block slot="backText">返回</block>
			</cu-custom>
		</view>
		<form>
			<view class="cu-form-group">
				<view class="title">商品标题</view>
				<input name="input" :value="storeDetail.title" disabled class="text-grey"></input>
			</view>

			<view class="cu-form-group">
				<view class="title">商品类型</view>
				<input name="input" :value="storeDetail.classify" disabled class="text-grey"></input>
			</view>
			<view class="cu-form-group">
				<view class="title">免费赠送</view>
				<switch @change="switchChange" :checked="isFree?true:false"></switch>
			</view>

			<view class="cu-form-group">
				<view class="title">商品售价</view>
				<input placeholder="请输入商品售价" @input="priceInput" name="input" type="number" v-model="price"></input>
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
			<view class="cu-form-group">
				<view class="title">
					图片
				</view>
			</view>
			<view class="cu-form-group align-start">
				<view class="title">描述</view>
				<textarea placeholder="详细描述一下商品" v-model="description"></textarea>
			</view>
			<view class="padding flex flex-direction">
				<button open-type="getUserInfo" class="cu-btn bg-gradual-orange lg" @click="confirmUpdate">确认修改</button>
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
				storeDetail: '',
				isFree: '',
				price: 0,
				connection: "",
				phone: '',
				weixin: "",
				description: "",
				noUpdate: true
			};
		},
		methods: {
			// 填写信息错误提示
			wxShowMessage(type, icon, content) {
				const that = this
				wx.lin.showMessage({
					type: type,
					icon: icon,
					content: content,
					success() {
						that.noUpdate = true
					}
				})
			},
			// 修改
			confirmUpdate() {
				const that = this
				// 判断是不是已经修改过了
				if (this.isFree === this.storeDetail.isFree && this.price === this.storeDetail.price && this.connection === this.storeDetail
					.connection && this.phone === this.storeDetail.phone && this.weixin === this.storeDetail.weixin && this.description ===
					this.storeDetail.description) {
					this.noUpdate = false
					this.wxShowMessage('error', 'error', "修改失败，没有信息被更改")
				} else if (!this.connection || !this.phone || !this.weixin || !this.description || (this.price == null && this.isFree ==
						false)) {
					this.noUpdate = false
					this.wxShowMessage("error", "error", "填写信息不完整")
				} else if (this.phone.length !== 11) {
					// console.log("请填写正确的手机号码")
					this.noUpdate = false
					this.wxShowMessage("error", "error", "请填写正确的手机号码")
				} else if (this.price < 0) {
					this.noUpdate = false
					this.wxShowMessage("error", "error", "请输入正确的价格")
				} else {
					// 修改
					wx.lin.showDialog({
						type: "confirm",
						content: "确定修改吗？",
						showTitle: false,
						success: (res) => {
							if (res.confirm) {
								uni.showLoading({
									title: '修改中...',
									mask: true
								})
								uniCloud.callFunction({
									name: 'updateMessage',
									data: {
										_id: that.storeDetail._id,
										price: that.price,
										phone: that.phone,
										weixin: that.weixin,
										description: that.description,
										connection: that.connection,
										isFree: that.isFree
									},
									success(res) {
										uni.hideLoading()
										uni.navigateBack({
											delta: 1
										})
										uni.$emit('changeDetail', {
											price: that.price,
											_id: that.storeDetail._id,
											isFree: that.isFree,
											phone: that.phone,
											weixin: that.weixin,
											description: that.description,
											connection: that.connection
										})
									}
								})

							}
						}
					})





				}
			},
			// 按钮的改变
			switchChange(e) {
				if (e.target.value === true) {
					this.isFree = !this.isFree;
					this.price = 0
				} else {
					this.price = null
					this.isFree = !this.isFree
				}
			},
			// 价格输入
			priceInput() {
				if (this.price == 0) {
					this.isFree = true
				} else {
					this.isFree = false
				}
			},
		},
		// 接收商品的个人信息
		onLoad: function(option) {
			const that = this
			let data = JSON.parse(decodeURIComponent(option.data))
			this.storeDetail = data
			this.isFree = data.isFree
			this.price = data.price
			this.connection = data.connection
			this.phone = data.phone
			this.weixin = data.weixin
			this.description = data.description
		}
	}
</script>

<style lang="scss">
	.text-grey,
	.line-grey,
	.lines-grey {
		color: #b6b6b6;
	}
</style>
