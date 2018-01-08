<template>
	<div class="bank">
		<div class="text_top">银行限额随银行变动会进行调整，以银行限额为准</div>
			<div class="ul_bank">
				<ul id="bank_ul">
					<li v-for="(bank,index) in bankList" @click="selectFn(bank,index)" :class="[{on: i==index},{on: bank.paymentCode==banks}]">
						<span :class="bank.paymentCode"></span>
						<div>
							<h4>{{bank.name}}</h4>
							<p>单笔限额{{bank.limitOneTime}}万 单日限额{{bank.limitOneDay}}万 每月限额{{bank.limitOneMonth}}万</p>
						</div>
					</li>
				</ul>
			</div>
	</div>
</template>
<script type="text/ecmascript-6">
	export default {
		data () {
			return {
				i: -1,
				banks: '',
				bankList: [],
				accessId: '0b33346e448cb2e5d3c8a743e7088377',
				accessKey: 'zji3mzmxnwe0zjhimtiwyjkyzthlmgrjyjuwm2eyzmi=',
				apiUrl: 'api/router/recharge/bankList'
			}
		},
		mounted() {
			this.init()
		},
		methods: {
			init() {
				let vm = this
				vm.$http({url: vm.apiUrl, method: 'post', headers: {accessId: vm.accessId, accessKey: vm.accessKey}})
								.then((response) => {
									vm.bankList = response.body.content
								})
				vm.banks = vm.$route.query.bank
				if (/(iPhone|iPad|iPod|iOS)/i.test(navigator.userAgent)) {
					vm.setupWebViewJavascriptBridge(function (bridge) {
						bridge.callHandler('h5ToNative_GetUserInfo', {}, function (response) {
							vm.accessId = response.accessId
							vm.accessKey = response.accessKey
							vm.$http({url: vm.apiUrl, method: 'post', headers: {accessId: vm.accessId, accessKey: vm.accessKey}})
								.then((response) => {
									vm.bankList = response.body.content
								})
						})
					})
				} else if (/(Android)/i.test(navigator.userAgent)) {
					setTimeout(function() {
						window.TDBridge.h5ToNative_GetUserInfo(function(data) {
							data = JSON.parse(data)
							vm.accessId = data.accessId
							vm.accessKey = data.accessKey
							vm.$http({url: vm.apiUrl, method: 'post', headers: {accessId: vm.accessId, accessKey: vm.accessKey}})
								.then((response) => {
									vm.bankList = response.body.content
								})
						})
					}, 500)
				}
			},
			selectFn (bank, index) {
				this.i = index
				this.banks = ''
				if (/(iPhone|iPad|iPod|iOS)/i.test(navigator.userAgent)) {
					this.setupWebViewJavascriptBridge(function (bridge) {
						bridge.callHandler('h5ToNative_BankInfo', {
							'bankName': bank.name,
							'bankCode': bank.paymentCode
						}, function (response) {
						})
					})
				} else if (/(Android)/i.test(navigator.userAgent)) {
					setTimeout(function() {
						window.TDBridge.h5ToNative_BankInfo({
							'bankName': bank.name,
							'bankCode': bank.paymentCode
						})
					}, 500)
				}
			},
			setupWebViewJavascriptBridge(callback) {
				if (window.WebViewJavascriptBridge) {
					return callback(window.WebViewJavascriptBridge)
				}
				if (window.WVJBCallbacks) {
					return window.WVJBCallbacks.push(callback)
				}
				window.WVJBCallbacks = [callback]
				let WVJBIframe = document.createElement('iframe')
				WVJBIframe.style.display = 'none'
				WVJBIframe.src = 'wvjbscheme://__BRIDGE_LOADED__'
				document.documentElement.appendChild(WVJBIframe)
				setTimeout(function () {
					document.documentElement.removeChild(WVJBIframe)
				}, 0)
			}
		}
	}
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
	@import "~common/stylus/variable"
	.bank
		max-width: 414px
		margin: 0 auto
		.text_top
			height:0.64rem
			background-color: #f2f3f7
			font-size: 0.2rem
			color: #626262
			line-height: 0.64rem
			text-align:center
		.ul_bank
			li
				border-bottom: 1px solid #e4e4e4
				height:1.3rem
				position: relative
				padding:0 0.13rem
				background-color: $color-background-f
				overflow: hidden
				div
					height: 0.8rem
					float: left
					line-height: 0.4rem
					padding-top: 0.3rem
					h4
						font-size: 0.28rem
						color: #626262
					p
						font-size: 0.24rem
						color: #b6b5b6
						margin-top:0.05rem
				span
						display: inline-block
						width: 1rem
						height: 1rem
						float: left
						margin:0.19rem 0.2rem 0 0
			.on
				background: #fff url(../../image/select_bank/yes.png) no-repeat 96% 60%
				background-size: 5%
			.ICBC
				background: url(../../image/select_bank/ICBC.png) no-repeat 50% 60%
				background-size: 65%
			.CCB
				background: url(../../image/select_bank/CCB.png) no-repeat 50% 60%
				background-size: 65%
			.BOC
				background: url(../../image/select_bank/BOC.png) no-repeat 50% 60%
				background-size: 65%
			.ABC
				background: url(../../image/select_bank/ABC.png) no-repeat 50% 60%
				background-size: 65%
			.CMB
				background: url(../../image/select_bank/CMB.png) no-repeat 50% 50%
				background-size: 70%
			.CITIC
				background: url(../../image/select_bank/CITIC.png) no-repeat 50% 60%
				background-size: 65%
			.SPDB
				background: url(../../image/select_bank/SPDB.png) no-repeat 50% 60%
				background-size: 65%
			.CIB
				background: url(../../image/select_bank/CIB.png) no-repeat 50% 60%
				background-size: 65%
			.CMBC
				background: url(../../image/select_bank/CMBC.png) no-repeat 50% 60%
				background-size: 65%
			.CEB
				background: url(../../image/select_bank/CEB.png) no-repeat 50% 60%
				background-size: 65%
			.PAB
				background: url(../../image/select_bank/PAB.png) no-repeat 50% 60%
				background-size: 65%
			.HXB
				background: url(../../image/select_bank/HXB.png) no-repeat 50% 60%
				background-size: 65%
			.CGB
				background: url(../../image/select_bank/CGB.png) no-repeat 50% 60%
				background-size: 65%
			.PSBC
				background: url(../../image/select_bank/PSBC.png) no-repeat 50% 60%
				background-size: 65%
			.BCM
				background: url(../../image/select_bank/BCM.png) no-repeat 50% 60%
				background-size: 65%
</style>