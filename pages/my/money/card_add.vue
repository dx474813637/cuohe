<template>
	<view class="w u-p-20">
		<view class="w-bg"></view>
		<u-navbar
			bgColor="transparent"
			:fixed="false"
			title="资金平台"
			titleStyle="color: #fff"
			autoBack
		>
			<view slot="left">
				<view class="text-white u-flex u-flex-items-center">
					<u-icon
						name="arrow-left"
						color="#fff"
						size="20"
					></u-icon> 
				</view>
				
			</view> 
		</u-navbar>
		<view class="main u-p-10 u-p-t-20">
			<view class="text-white u-p-b-30 u-p-t-10 u-font-32 info-box u-flex u-flex-items-center u-flex-column">
				<view class="u-font-40">{{tishi.list[3].title}}</view>
				<view class="u-font-28 u-p-t-10" style="opacity: 0.8; font-weight: 300">{{tishi.list[3].info}}</view>
				
			</view> 
			<view class="form-w u-m-t-30"> 
				<view class="form-content u-p-l-40 u-p-r-40 u-p-20">
					<u--form
						:model="model"
						:rules="rules"
						ref="from"
						labelWidth="100%"
						labelPosition="top"
						:labelStyle="{color: '#777'}"
					>
						<u-form-item
							label="账户名"
							prop="name"
							ref="name"
						>
							<u--input
								:value="model.name"
								readonly
								border="none" 
								clearable
							></u--input>
						</u-form-item>
						<u-form-item
							label="请输入卡号"
							prop="bank_accno"
							ref="bank_accno"
							required
						>
							<!-- <view> -->
								<u--input
									v-model="model.bank_accno"
									clearable
									placeholder="请输入银行卡号"
									border="bottom"
								></u--input>
								<!--
									@change="$u.debounce(handleBankChange, 2500)" 
								<view class="u-p-t-20 u-flex u-flex-items-center "
									:class="{
										'text-error': isbankInfo.status == 'error',
										'text-light': isbankInfo.status != 'error'
									}"
								>
									<image :src="isbankInfo.logo" mode="" style="width: 25px; height: 25px;"></image>
									<text class="u-p-l-20" v-if="!bankLoading">{{isbankInfo.name}}</text>
									<u-loading-icon mode="semicircle" size="20" v-else></u-loading-icon>
								</view>
							</view> -->
							
						</u-form-item>
						<u-form-item
							label="社会信用统一代码"
							prop="card_id"
							ref="card_id"
							required
						>
							<u--input
								v-model="model.card_id"
								border="bottom"
								placeholder="请输入社会信用统一代码"
								clearable
							></u--input>
						</u-form-item>
						<u-form-item
							label="法人姓名"
							prop="legal_name"
							ref="legal_name"
							required
						>
							<u--input
								v-model="model.legal_name"
								border="bottom"
								placeholder="请输入法人姓名"
								clearable
							></u--input>
						</u-form-item>
						<u-form-item
							label="法人身份证"
							prop="lecerti_code"
							ref="lecerti_code"
							required
						>
							<u--input
								v-model="model.lecerti_code"
								border="bottom"
								placeholder="请输入法人身份证"
								clearable
							></u--input>
						</u-form-item>
						<u-form-item
							label="手机号"
							prop="mobile"
							ref="mobile"
							required
						>
							<view class="">
								<u--input
									v-model="model.mobile"
									border="bottom"
									placeholder="请输入该卡在银行预留的手机号"
									clearable
								></u--input>
								<!-- <view class="u-p-t-20 text-light2">手机号码必须与银行预留手机号一致，绑定后您的资金账户手机号码也会变成该手机号码</view> -->
							</view>
						</u-form-item>
						
						<u-form-item
							label="开户行"
							prop="bank_name"
							ref="bank_name"
							required
						>
							<view @click="show = true">
								<u--input
									:value="model.bank_name"
									readonly
									border="bottom"
									suffixIcon="arrow-down"
									suffixIconStyle="color: #909399"
									placeholder="点击搜索开户行"
									clearable
								></u--input>
							</view>
							
						</u-form-item>
					</u--form>
					
					<view class="u-p-t-20 u-m-b-20">
						<u-button type="primary" shape="circle" :customStyle="{backgroundImage: 'linear-gradient(to right,#6cb5fa, #4077f6)'}" @click="submit">{{tishi.list[3].button || '提交'}}</u-button>
					</view>
					<view class="form-w u-font-28 u-m-b-20 text-base u-text-center" v-if="tishi.list[2].other">
						<u-parse :content="tishi.list[2].other"></u-parse>
					</view>
				</view>
			</view>
		</view>
		
		<menusPopupBank 
			:show="show" 
			theme="white" 
			showMode="list"
			@close="show = false"
			@confirm="menusConfirm"
		></menusPopupBank>
		
		
		<u-modal :show="codeInputShow" negativeTop="220" :title="textObj.title"
			showCancelButton
			cancelText="账户银行卡列表"
			cancelColor="#999"
			@cancel="handleBack"
			confirmText="当前银行卡详情" 
			@confirm="handleBackDetail"
			>
			<view class="slot-content u-p-t-10">
				<view class="u-m-b-20 text-light2 u-font-28">
					如跳过当前步骤，后续可在账户银行卡详情中填写此次弹窗表单
				</view>
				<u--form
					:model="model_yanzheng"
					:rules="rules_yanzheng"
					ref="from_yanzheng"
					labelWidth="100%"
					labelPosition="top"
					:labelStyle="{color: '#777'}"
				>
					<u-form-item
						label="鉴权验证金额"
						prop="amt"
						ref="amt"
						required 
					>
						<view>
							<u--input
								v-model="model_yanzheng.amt" 
								placeholder="鉴权验证金额（银行卡内查询）"
								clearable
							></u--input>
							<view class="u-font-28 u-info u-m-t-10">鉴权有效时间是48小时</view>
						</view>
					</u-form-item>
					<u-form-item
						label="鉴权序号"
						prop="code"
						ref="code"
						required 
					>
						<view>
							<u--input
								v-model="model_yanzheng.code" 
								placeholder="鉴权序号（手机短信）"
								clearable
							></u--input>
							<view class="u-font-28 u-m-t-10">
								<text class="u-info">如长时间未收到鉴权序号，可点击取消该鉴权任务，重新发起绑定</text>
								<text class="text-error u-m-l-10" @click="check_cancel">点我取消鉴权</text>
							</view>
						</view>
					</u-form-item>
				</u--form> 
				<view class="u-m-t-40">
					<u-button type="primary" @click="submit_yanzheng">提交 验证</u-button>
				</view>
			</view>
		</u-modal>
	</view>
</template>

<script>
	import {mapState, mapGetters, mapMutations, mapActions} from 'vuex'
	export default {
		data() {
			return {
				tips: '',
				show: false,
				disabled: true,
				codeInputShow: false,
				type: 'b',
				from_wallet: 'B',
				code: '',
				model: {
					account_id: '',
					name: '',
					bank_accno: '',
					card_id: '',
					legal_name: '',
					lecerti_code: '',
					mobile: '',
					bank_name: ''
				},
				model_yanzheng: {
					id: '',
					amt: '',
					code: ''
				},
				bankLoading: false,
				isbankInfo: {
					logo: 'https://65180.m.toocle.com/Public/nocard.png',
					name: '',
					status: 'error'
				},
				rules: {
					bank_accno: {
						type: 'string',
						required: true,
						message: '请填写银行卡号',
						trigger: ['blur', 'change']
					}, 
					card_id: {
						type: 'string',
						required: true,
						message: '请填写社会信用统一代码',
						trigger: ['blur', 'change']
					}, 
					legal_name: {
						type: 'string',
						required: true,
						message: '请填写法人姓名',
						trigger: ['blur', 'change']
					}, 
					lecerti_code: {
						type: 'string',
						validator: (rule, value, callback) => { 
							return uni.$u.test.idCard(value)
						},
						message: '请填写正确的身份证',
						trigger: ['blur', 'change']
					}, 
					mobile: {
						type: 'string',
						validator: (rule, value, callback) => { 
							return uni.$u.test.mobile(value)
						},
						message: '请填写正确的手机号',
						trigger: ['blur', 'change']
					}, 
					bank_name: {
						type: 'string',
						required: true,
						message: '请匹配开户行',
						trigger: ['blur', 'change']
					}, 
				},
				rules_yanzheng: {
					amt: {
						type: 'string',
						required: true,
						message: '请填写鉴权验证金额',
						trigger: ['blur', 'change']
					}, 
					code: {
						type: 'string',
						required: true,
						message: '请填写鉴权序号',
						trigger: ['blur', 'change']
					},
				}
			}
		},
		onReady() {
			this.$refs.from.setRules(this.rules)
			this.$refs.from_yanzheng.setRules(this.rules_yanzheng)
		},
		computed: {
			...mapState({ 
				sino: state => state.sinopay.sino,
				myCpy: state => state.user.myCpy,
				sinoFund: state => state.sinopay.sinoFund,
				tishi: state => state.sinopay.tishi,
			}),
			textObj() {
				if(this.type == 'c') {
					return {
						title: '个人验证',
						sub: '输入短信验证码'
					}
				}else {
					return {
						title: '企业验证',
						sub: '输入鉴权序号'
					}
				}
			},
			
		},
		async onLoad(options) { 
			if(options.hasOwnProperty('wallet')) {
				this.from_wallet = options.wallet
			}
			this.model.account_id = this.sinoFund.filter(ele => ele.type == this.from_wallet)[0]?.id
			this.model.name = this.myCpy.name
			this.model.card_id = this.myCpy.credit_code
			this.model.mobile = this.myCpy.mobile
		},
		methods: {
			// codeChange(text) {
			// 	this.tips = text;
			// },
			menusConfirm(data) {
				this.model.bank_name = data.bank_name 
				this.$refs.from.validateField('bank_name')
			},
			handleBack() {
				uni.redirectTo({
					url: '/pages/my/money/bank_card'
				})
			},
			handleBackDetail() {
				uni.redirectTo({
					url: '/pages/my/money/bank_card_detail?id=' + this.model_yanzheng.id
				})
			},
			handleCloseModal() {
				
			},
			check_cancel() {
				uni.showModal({
					title: '取消鉴权提示',
					content: '是否取消当前银行卡鉴权',
					success:  async (r) => {
						if (r.confirm) {
							uni.showLoading()
							const res = await this.$api.sino_fund_account_check_cancel({
								params: {
									id: this.model_yanzheng.id
								}
							})
							if(res.code == 1) {
								uni.showToast({
									title: res.msg,
									icon: 'none',
									success: () => {
										this.handleGoto('/pages/my/money/index_new')
									}
								})
							}
						} else if (r.cancel) {
							console.log('用户点击取消');
						}
					}
				});
			},
			submit_yanzheng() {
				this.$refs.from_yanzheng.validate().then(async res => {
					uni.showLoading()
					const r = await this.$api.sino_fund_account_check({
						params: {...this.model_yanzheng}
					})
					console.log(r)
					if(r.code == 1) { 
						uni.showToast({
							title: r.msg,
							icon: 'none',
							success: () => {
								uni.redirectTo({
									url: '/pages/my/money/bank_card_detail?id=' + this.model_yanzheng.id
								})
							}
						})
					}
				}).catch(errors => {
					uni.$u.toast('校验失败')
				})
			},
			submit() {
				
				this.$refs.from.validate().then(async res => {
					uni.showLoading()
					const r = await this.$api.sino_fund_account_bind({
						params: {
							...this.model
						}
					})
					console.log(r)
					if(r.code == 1) {
						if(r.list.state == '1') {
							uni.showToast({
								title: r.msg,
								icon: 'success',
								success: () => {
									setTimeout(() => {
										uni.reLaunch({
											url: '/pages/my/money/index_new'
										})
									}, 1500);
									
								}
							})
							
							return
						}else if(r.list.state == '0') {
							this.codeInputShow = true
							this.model_yanzheng.id = r.id
							
						} 
						uni.showToast({
							title: r.msg,
							icon: 'none'
						})
						
						// this.$nextTick(() => {
						// 	this.focus = true
						// 	this.$refs.uCode.start();
						// })
						
						// setTimeout(() => {
						// 	uni.navigateBack()
						// }, 800)
					}
				}).catch(errors => {
					uni.$u.toast('校验失败')
				})
			}
		}
	}
</script>

<style lang="scss">
	.u-page {
		&__code-text {
			color: $u-primary;
			font-size: 15px;
			padding-top: 10px;
		}
	}
	page {
		background-color: $page-bg2;
	}
</style>

<style scoped lang="scss">
	
	.w {
		box-sizing: border-box;
		// position: absolute;
		// z-index: 1;
		min-height: 100vh;
		// background-image: url("https://wx.rawmex.cn/Public/bg1.png");
		// background-size: 100% 240px;
		// background-position: top;
		// background-repeat: no-repeat;
		width: 100%;
		// height: 240px;
		// left: 0;
		// top: 0;
	}
	.w-bg {
		position: absolute;
		left: 0;
		top: 0;
		height: 240px;
		background: #3f5297;
		width: 100%;
	}
	.main {
		position: relative;
		z-index: 1;
		box-sizing: border-box;
	}
	.form-w {
		box-sizing: border-box;
		border-radius: 10px;
		overflow: hidden;
		.form-content {
			background-color: #fff;
		}
	}
</style>
