<template>
	<view class="w u-p-20">
		<view class="bg" :style="{
			backgroundColor: configObj.themeColor
		}"></view>
		<u-navbar
			bgColor="transparent"
			:fixed="false"
		>
			<view slot="left">
				<view @click="handleGoto('/pages/my/money/index_new')" class="text-white u-flex u-flex-items-center">
					<u-icon
						name="arrow-left"
						color="#fff"
					></u-icon>
					<text class="u-p-l-20 ">资金平台</text>
				</view>
				
			</view>
		</u-navbar>
		<view class="main u-p-10 u-p-t-20">
			<view class="text-white u-p-b-10 u-font-36">
				{{configObj.title}}
			</view>
			<view class="text-white u-font-28 u-p-b-30">
				{{configObj.sub}}
			</view>
			<view class="form-w u-p-20 u-p-l-40 u-p-r-40">
				<u--form
					labelPosition="top"
					:model="model"
					:rules="rules"
					ref="form"
					labelWidth="100%"
					borderBottom
					:labelStyle="{fontSize: '28rpx', paddingBottom: '10rpx'}"
				>
					<u-form-item
						label="资金账户" 
						required
					>
						<view @click="showPicker">
							<u--input
								:value="sino_acc"
								readonly
								suffixIcon="arrow-down"
								suffixIconStyle="color: #bbb" 
							></u--input>
						</view>
						
					</u-form-item>
					
					<u-picker 
						:show="show_acc" 
						ref="uPicker" 
						closeOnClickOverlay
						:columns="columns_acc" 
						@confirm="confirm_acc" 
						@close="show_acc = false" 
						@cancel="show_acc = false" 
						></u-picker>
					
					<u-form-item
						label="充值类型"
						@click="showActions"
						ref="item1"
						:borderBottom="false"
						v-if="cz == 1"
						>
						<u-radio-group 
						    v-model="cztype"
						    placement="row">
							<u-radio label="直接充值" name="直接充值"></u-radio>
							<view class="u-p-l-20"></view>
							<u-radio label="预约充值" name="预约充值"></u-radio>
						</u-radio-group>
					</u-form-item>
					<template v-if="cz == 0">
						<view class="u-p-t-20 u-p-b-20" >
							<view class="u-flex u-flex-items-center u-m-b-30">
								<text class="text-light u-font-28">可提金额：</text>  
								<view class="u-p-l-10" >
									<u--text color="#f00" mode="price" :text="sinoFund[index_acc].bal || 0"></u--text>
								</view>
								
							</view>  
						</view>
						<u-form-item
							:label="`选择${cz == 1? '充值' : '提现'}银行卡`"
							prop="bank_accid"
							ref="form_bank_accid"
							required
						>
							<view @click="handleSelectBankcard" style="position: relative;">
								<u--input
									v-model="bank.name"
									placeholder="点击选择银行卡"
									suffixIcon="arrow-down"
									suffixIconStyle="color: #bbb"
									readonly
								></u--input>
								<view class="loading-w u-flex u-flex-items-center u-flex-center" v-if="bankLoading" >
									<u-loading-icon mode="circle" ></u-loading-icon>
								</view>
							</view>
							
						</u-form-item>
					</template>
					<view class="u-p-t-20 u-p-b-20" v-if="cz == 2">
						<view class="u-flex u-flex-items-center u-m-b-30">
							<text class="text-light u-font-28">转出资金账户：</text>
							<view><u--text :text="tmzzData.from.user_fundaccno"></u--text></view> 
							<view class="bg-primary u-radius-5 u-p-l-10 u-p-r-20 u-m-l-25" >
								<u--text color="#fff" mode="price" :text="tmzzData.from.bal || 0"></u--text>
							</view>
							
						</view> 
						<view class="u-flex u-flex-items-center u-m-b-10">
							<text class="text-light u-font-28">转入资金账户：</text>
							<view><u--text :text="tmzzData.to.user_fundaccno"></u--text></view> 
							<view class="bg-primary u-radius-5 u-p-l-10 u-p-r-20 u-m-l-25" >
								<u--text color="#fff" mode="price" :text="tmzzData.to.bal || 0"></u--text>
							</view>
						</view>
					</view>
					
					<u-form-item
						:label="`${configObj.label}金额`"
						prop="money"
						ref="form_money"
						required
					>
						<u--input
							v-model="model.money"
							placeholder="请输入金额"
							clearable
							type="digit"
							@change="$u.debounce(handleMoneyChange, 800)"
						></u--input>
					</u-form-item>
					
					<view class="u-p-t-20 u-p-b-20">
						<template v-if="cz != 2">
							<view class="u-flex u-flex-items-center u-m-b-10">
								<text class="text-light u-font-28">手续费：</text>
								<u-loading-icon mode="circle" v-if="sxfLoading" size="18"></u-loading-icon>
								<u--text mode="price" :text="sxf"></u--text>
								
							</view>
							<view class="u-flex u-flex-items-center">
								<text class="text-light u-font-28">到账金额：</text>
								<u--text mode="price" :text="money"></u--text>
							</view>
						</template>
						<template v-else>
							<!-- <text class="text-error u-font-28">当天可提，转账金额需要小于等于可提余额 ??</text> -->
						</template>
					</view>
					
					<u-form-item
						v-if="cz != 1"
						:label="`备注`"
						prop="bz"
						ref="form_bz"
					>
						<u--input
							v-model="model.bz"
							placeholder="备注"
							clearable
						></u--input>
					</u-form-item>
					
				</u--form>
				<view class="u-p-t-50 u-m-b-40">
					<u-button type="primary" :customStyle="{
						backgroundColor: configObj.themeColor,
						borderColor: configObj.themeColor,
					}" @click="submit">提交</u-button>
				</view>
			</view>
		</view>
		<u-popup 
			:show="bankPopup" 
			@close="bankClose" 
			@open="bankOpen"
			mode="bottom"
		>
			<view class="popup-wrapper">
				<view class="wrapper-title u-p-r-40 text-light u-flex u-flex-items-center u-flex-between">
					<text></text>
					<text>选择银行卡</text>
					<i @click="refreshBankList" class="custom-icon-refresh custom-icon"></i>
				</view>
				<view class="wrapper-main">
					
					<template v-if="bankLoading">
						<view class="loading-w u-flex u-flex-items-center u-flex-center" :style="{
							backgroundColor: typeConfig.white.mask,
						}">
							<u-loading-icon mode="circle" size="36" text="获取最新数据中..." vertical></u-loading-icon>
						</view>
					</template>
					<u-list
						height="100%" 
						enableBackToTop
						@scrolltolower="scrolltolower"
						:preLoadingScreen="100"
					>
						<u-list-item
							v-for="(item, index) in indexList"
							:key="item.id"
						>
							<view class="u-p-10">
								<BankCard
									:bank_class="item.bank_class"
									:bank_name="item.bank_name"
									:bank_sub="item.bank_accname"
									:bank_no="item.bank_accno"
									:origin="item"
									@detail="handleClick"
								></BankCard> 
							</view>
							
						</u-list-item> 
					
						<template name="dataStatus">
							<template v-if="indexList.length == 0">
								<template v-if="loadstatus == 'loading'">
									<view class="u-p-80">
										<u-loading-icon></u-loading-icon>
									</view>
								</template>
								<template v-else>
									<u-empty
										mode="data"
										:icon="typeConfig.white.empty"
									>
									</u-empty>
								</template>
								
							</template>
							<!-- <template v-else>
								<u-loadmore
									:status="loadstatus"
								/>
							</template> -->
						</template>
					
					</u-list>
				</view>
				<view class="wrapper-footer">
					<view @click="handleGoto({url: '/pages/my/money/card_add', params: { wallet: sinoFund[index_acc].type }})" class="footer-a u-flex u-flex-items-center u-flex-center">
						<u-icon name="plus-circle-fill" color="#f00"></u-icon>
						<text class="u-p-l-10 text-error">添加新的银行卡</text>
					</view>
				</view>
			</view>
		</u-popup>
		<u-modal :show="codeInputShow" negativeTop="220" title="输入支付密码"  
			showCancelButton
			cancelText="返回资金平台"
			cancelColor="#999"
			@cancel="handleBack"
			:confirmText="`当前${configObj.label}订单详情`" 
			@confirm="handleBackDetail"
			>
			<view class="slot-content u-p-t-10">
				<view class="u-m-b-20 text-light2 u-font-28">
					如跳过当前步骤，后续可在账户{{configObj.label}}详情中验证
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
						label="支付密码"
						prop="paypwd"
						ref="paypwd"
						required 
					> 
						<u--input
							v-model="model_yanzheng.paypwd" 
							placeholder="支付密码"
							clearable
							type="password"
						></u--input> 
					</u-form-item>
				</u--form> 
				<view class="u-m-t-40">
					<u-button type="primary" @click="submit_yanzheng">提交 验证</u-button>
				</view> 
				
			</view>
		</u-modal>
		<!-- 
			closeOnClickOverlay
			@close="codeInputShow_code = false" -->
		<u-modal :show="codeInputShow_code" negativeTop="220" title="短信验证码校验" 
			showCancelButton
			cancelText="返回资金平台"
			cancelColor="#999"
			@cancel="handleBack"
			:confirmText="`账户${configObj.label}列表`" 
			@confirm="handleGoZz"
			>
			<view class="slot-content u-p-t-10">
				<view class="u-m-b-20 text-light2 u-font-28">
					如跳过当前步骤，后续可在同名账户{{configObj.label}}列表中验证
				</view>
				<u--form
					:model="model_yanzheng_code"
					:rules="rules_yanzheng_code"
					ref="from_yanzheng_code"
					labelWidth="100%"
					labelPosition="top"
					:labelStyle="{color: '#777'}"
				>
					<u-form-item
						label="短信验证码"
						prop="captcha"
						ref="captcha"
						required 
					> 
						<u-input
							v-model="model_yanzheng_code.captcha" 
							placeholder="短信验证码"
							clearable
						>
							<template slot="suffix">
								<u-code
									ref="uCode"
									@change="codeChange"
									seconds="60"
									changeText="X秒重新获取"
								></u-code>
								<u-button
									@tap="getCode"
									:text="tips"
									type="success"
									size="mini"
								></u-button>
							</template>
						</u-input> 
					</u-form-item>
				</u--form> 
				<view class="u-m-t-40">
					<u-button type="primary" @click="submit_yanzheng_code">提交验证码</u-button>
				</view> 
				
			</view>
		</u-modal>
		<u-toast ref="uToast"></u-toast>
	</view>
</template>

<script>
	import {mapState, mapGetters, mapMutations, mapActions} from 'vuex'
	import BankCard from '@/pages/my/components/BankCard/BankCard.vue'
	export default {
		data() {
			return {
				tips: '',
				show_acc: false,
				index_acc: 0,
				from_wallet: 'B',
				cz: 1,
				cztype: '直接充值',
				bankPopup: false,
				codeInputShow: false,
				codeInputShow_code: false,
				code: '',
				sxf: 0,
				bank: {
					id: '',
					name: ''
				},
				model: { 
					money: '',
					bank_accid: '',
					bz: ''
				},
				model_yanzheng: { 
					id: '',
					paypwd: '',
				},
				model_yanzheng_code: { 
					id: '',
					captcha: '',
				},
				indexList: [],
				curP: 1,
				loadstatus: 'loadmore',
				bankLoading: false
			}
		},
		components: {
			BankCard
		},
		async onLoad(options) {
			if(options.hasOwnProperty('cz')) {
				this.cz = options.cz
			}
			if(options.hasOwnProperty('wallet')) {
				this.from_wallet = options.wallet
			}
			if(!this.sinoFund || this.sinoFund.length == 0) {
				await this.getSinoFundAccount()
			}
			this.index_acc = this.sinoFund.findIndex(ele => ele.type == this.from_wallet)
			this.refreshBankList()
		},
		// watch: {
		// 	index_acc: {
		// 		// immediate: true,
		// 		async handler(n) {
		// 			this.refreshBankList()
		// 		}
		// 	}
		// },
		computed: {
			...mapState({
				typeConfig: state => state.theme.typeConfig,
				sinoFund: state => state.sinopay.sinoFund,
				sinoFundLoading: state => state.sinopay.sinoFundLoading,
			}),
			columns_acc() {
				if(!this.sinoFund || this.sinoFund.length == 0) return [];
				return [
					this.sinoFund.map(ele => {
						let label = '';
						if(ele.type == 'S') label = '【收】'
						else label = '【付】'
						return `${label}${ele.user_fundaccno}-${ele.name}`
					})
				]
			},
			tmzzData() {
				if(!this.sinoFund || this.sinoFund.length == 0) return {};
				return {
					from: this.sinoFund[this.index_acc],
					to:  this.sinoFund[1-this.index_acc],
				}
			},
			sino_acc() {
				if(!this.sinoFund || this.sinoFund.length == 0) return '';
				let item = this.sinoFund[this.index_acc]
				let label = ''; 
				if(item.type == 'S') label = '【收】'
				else label = '【付】'
				return `${label}${item.user_fundaccno}-${item.name}`
			},
			rules() {
				if(this.cz != '2') {
					return {
						'money': [{
							type: 'number',
							required: true,
							message: '请填写金额数值',
							trigger: ['blur', 'change']
						},{
							validator: (rule, value, callback) => {
								return value >= 0.01 && value <= Number(this.sinoFund[this.index_acc].bal)
							},
							message: '金额数值必须大于等于0.01且小于等于账户余额'
						}],
						'bank_accid': {
							type: 'string',
							required: true,
							message: '请选择银行卡',
							trigger: ['blur', 'change']
						}
					}
				}else {
					return {
						'money': [{
							type: 'number',
							required: true,
							message: '请填写金额数值',
							trigger: ['blur', 'change']
						},{
							validator: (rule, value, callback) => { 
								return value >= 0.01 && value <= Number(this.sinoFund[this.index_acc].bal)
							},
							message: '金额数值必须大于等于0.01且小于等于账户余额'
						}]
					}
				}
				
			},
			rules_yanzheng() {
				return {
					'paypwd': {
						type: 'string',
						required: true,
						message: '支付密码不得为空',
						trigger: ['blur', 'change']
					}, 
				}
			},
			rules_yanzheng_code() {
				return {
					'captcha': {
						type: 'string',
						required: true,
						message: '验证码不得为空',
						trigger: ['blur', 'change']
					}, 
				}
			},
			money() {
				if(!this.model.money) return 0
				return this.model.money - this.sxf
			}, 
			configObj() {
				if(this.cz == 1) {
					return {
						title: '我要充值',
						label: '充值',
						themeColor: '#d34c3c',
						sub: '',
						func_getbankcard: 'sino_fund_account_list_bind',
						func_sxf: '',
						func_create: '',
						func_paypwd: '',
					}
				}
				else if(this.cz == 0){
					return {
						title: '提现申请',
						label: '提现',
						themeColor: '#408df4',
						sub: '提现提前绑定好提现银行卡，且有可提余额。',
						func_getbankcard: 'sino_fund_account_list_bind',
						func_sxf: 'sino_fund_refund_fee_fund_count',
						func_create: 'sino_fund_refund_create',
						func_paypwd: 'sino_fund_refund_refund',
					}
				}
				else if(this.cz == 2){
					return {
						title: '转账到同名账户',
						label: '转账',
						themeColor: '#408df4',
						sub: '',
						func_create: 'sino_fund_account_tran_apply',
						func_code: 'sino_fund_account_tran', 
					}
				}
			}
		},
		onReady() {
			this.$refs.form.setRules(this.rules)
			this.$refs.from_yanzheng.setRules(this.rules_yanzheng)
			this.$refs.from_yanzheng_code.setRules(this.rules_yanzheng_code)
		},
		methods: {
			...mapMutations({
				handleGoto: 'user/handleGoto'
			}),
			...mapActions({
				getSinoFundAccount: 'sinopay/getSinoFundAccount'
			}),
			showToast(params) {
				this.$refs.uToast.show({
					position: 'bottom',
					...params, 
				})
			},
			async refreshBankList() {
				if(this.cz != 0) return
				this.bankLoading = true;
				this.initBankCardInfo()
				await this.getBankCard()
				this.bankLoading = false;
			},
			initBankCardInfo() {
				this.model.bank_accid = ''
				this.bank.name = ''
			},
			showPicker() {
				if(this.columns_acc.length == 0) return;
				this.show_acc = true
			},
			confirm_acc(e) { 
				this.show_acc = false
				if(this.index_acc != e.indexs[0]) {
					this.index_acc = e.indexs[0]
					this.refreshBankList()
				}
				
			},
			refreshList() {
				if(this.cz == '2') return
				this.initParamas()
				this.getData()
			},
			initParamas() {
				this.curP = 1;
				this.indexList = [];
				this.loadstatus = 'loadmore'
			},
			
			scrolltolower() {
				// this.getMoreData()
			},
			async handleMoneyChange() {
				if(this.model.money == 0) {
					this.sxf = 0
					return
				} 
				if(this.cz == 1) {
					// 充 
				}
				else if(this.cz == 0){
					// 提  
				}
				else if(this.cz == 2){
					// 转账 
				}
				if(!this.configObj.func_sxf) {
					this.sxf = 0
					return
				}
				this.sxfLoading = true
				const res = await this.$api[this.configObj.func_sxf]({
					params: {
						price: this.model.money
					}
				})
				this.sxfLoading = false
				if(res.code == 1) {
					this.sxf = res.list
				}
			},
			async getBankCard() {
				if( this.cz == '2') return
				// this.loadstatus = 'loading'
				const res = await this.$api.sino_fund_account_list_bind({
					params: {
						account_id: this.sinoFund[this.index_acc].id
					}
				})
				if(res.code == 1) {
					this.indexList = res.list.filter(ele => ele.state == '1')
					 
				}
			},
			async getMoreData() {
				if(this.loadstatus != 'loadmore') return
				this.curP ++
				await this.getData()
			},
			bankClose() {
				this.bankPopup = false
			},
			bankOpen() {
				
			},
			handleBack() {
				uni.redirectTo({
					url: '/pages/my/money/index_new'
				})
			},
			handleBackDetail() {
				uni.redirectTo({
					url: '/pages/my/money/sino_cz_detail?id=' + this.model_yanzheng.id
				})
			},
			handleGoZz() {
				uni.redirectTo({
					url: '/pages/my/money/sino_cz_list?tabs_current=2' 
				})
			},
			submit_yanzheng_code() {
				this.$refs.from_yanzheng_code.validate().then(async () => {
					uni.showLoading()
					const r = await this.$api.sino_fund_account_tran({
						params: {
							id: this.model_yanzheng_code.id,
							captcha: this.model_yanzheng_code.captcha, 
							flag: 2,
						}
					})
					if(r.code == 1) { 
						this.showToast({
							type: 'success',
							message: r.msg, 
						})
						this.codeInputShow_code = false
						this.handleGoZz()
					}
				})
			},
			submit_yanzheng() {
				this.$refs.from_yanzheng.validate().then(async res => {
					uni.showLoading()
					const r = await this.$api[this.configObj.func_paypwd]({
						params: {
							id: this.model_yanzheng.id,
							paypwd: this.model_yanzheng.paypwd, 
						}
					})
					if(r.code == 1) { 
						uni.showToast({
							title: r.msg,
							icon: 'none',
							success: () => {
								uni.redirectTo({
									url: '/pages/my/money/sino_cz_detail?id=' + this.model_yanzheng.id
								})
							}
						})
					}
				})
			},
			submit() {
				
				this.$refs.form.validate().then(async res => { 
					console.log(res)
					let params = {}
					if(this.cz == '2') {
						params = {
							b_user_fundaccno: this.tmzzData.from.user_fundaccno,
							s_user_fundaccno: this.tmzzData.to.user_fundaccno,
							price: this.model.money,
							remark: this.model.bz,
						}
					}else {
						params = {
							account_id: this.sinoFund[this.index_acc].id,
							bank_accid: this.model.bank_accid,
							price: this.model.money,
							remark: this.model.bz
						}
					}
					uni.showLoading()
					const r = await this.$api[this.configObj.func_create]({params})
					// const r = {
					// 	code: 1,
					// 	msg: '测试成功'
					// }
					console.log(r)
					if(r.code == 1) {
						if(this.cz != '2') {
							this.model_yanzheng.id = r.list.id
							this.codeInputShow = true
							uni.showToast({
								title: r.msg,
								icon: 'none'
							})
						}else {
							this.model_yanzheng_code.id = r.list.id
							uni.showModal({
								title: '提示',
								content: '创建转账订单成功！是否立即发送验证短信？',
								success: async (res) => {
									if (res.confirm) {
										this.codeInputShow_code = true 
										await this.getCode()
										
									} else if (res.cancel) {
										uni.redirectTo({
											url: '/pages/my/money/index_new'
										})
									}
								}
							});  
						}
					}
				}).catch(errors => {
					console.log(errors)
					uni.$u.toast('校验失败')
				})
			},  
			codeChange(text) {
				this.tips = text;
			},
			async getCode() {
				if (this.$refs.uCode.canGetCode) {
				  // 模拟向后端请求验证码
				  uni.showLoading({
					title: '正在获取验证码'
				  })
					const res = await this.$api.sino_fund_account_tran({
						params: {
							id: this.model_yanzheng_code.id,
							flag: 1,
						}
					})
					if(res.code == 1) {
						this.showToast({
							type: 'success',
							message: '发送成功！', 
						})
						 this.$refs.uCode.start();
					}
				} else {
				  uni.$u.toast('倒计时结束后再发送');
				}
			},
			handleSelectBankcard() {
				
				this.bankPopup = true
			},
			handleClick(bank) {
				console.log(bank)
				this.bank.name = `${bank.bank_accno}-${bank.bank_name}`
				this.model.bank_accid = bank.id
				this.bankClose()
			},
			handleBack() {
				uni.redirectTo({
					url: '/pages/my/money/index_new'
				})
			},
			async handleSumbitCode() {
				uni.showLoading()
				const res = await this.$api.valPhoneCode();
				if(res.code == 1) {
					uni.showToast({
						title: res.msg,
						icon: 'none'
					})
					setTimeout(() => {
						uni.redirectTo({
							url: '/pages/my/money/index_new'
						})
					}, 1000)
				}
			},
		}
	}
</script>
<style lang="scss">
	page {
		background-color: $page-bg2;
	}
</style>
<style scoped lang="scss">
	.loading-w {
		position: absolute;
		left: 0;
		top: 0;
		width: 100%;
		height: 100%;
		z-index: 10;
		background-color: rgba(255,255,255,.7);
	}
	.popup-wrapper {
		width: 100%;
		.wrapper-title {
			height: 90rpx;
		}
		.wrapper-main {
			border-top: 1rpx solid #f8f8f8;
			border-bottom: 1rpx solid #f8f8f8;
			height: 650rpx;
			width: 100%;
			background-color: $page-bg2;
			position: relative;
		}
		.wrapper-footer {
			height: 90rpx;
			.footer-a {
				width: 100%;
				height: 100%;
			}
		}
	}
	.bg {
		position: absolute;
		left: 0;
		top: 0;
		height: 240px;
		width: 100%;
		
	}
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
	.main {
		position: relative;
		z-index: 1;
		box-sizing: border-box;
	}
	.form-w {
		background-color: #fff;
		box-sizing: border-box;
		border-radius: 10px;
	}
</style>
