<template>
	<div class="result">
		<div class="result_top">
			<i class="iconfont">&#xe69c;</i>
			<p>{{type}}</p>
		</div>
		<div class="result_com">
			<ul>
				<li>
					<span>项目名称</span>
					<p>{{productTitle}}</p>
				</li>
				<li>
					<span>加入金额</span>
					<p>{{amount}}元</p>
				</li>
				<li>
					<span>到期收益</span>
					<p>{{preInterest}}元</p>
				</li>
				<li>
					<span>已用优惠券</span>
					<p>{{voucher}} {{voucherMoney}} {{voucherUnit}}</p>
				</li>
			</ul>
		</div>
		<div class="result_bot">
			<span class="invest_list" @click="goInvest()">继续加入</span><span class="add_list" @click="goInvestRecode()">查看投资记录</span>
		</div>
	</div>
</template>
<script type="text/ecmascript-6">
	export default {
		data () {
			return {
				type: '',
				productTitle: '',
				preInterest: '',
				amount: '',
				voucher: '',
				voucherUnit: '',
				voucherMoney: ''
			}
		},
		mounted() {
			this.init()
		},
		methods: {
			init() {
				let vm = this
				let intype = vm.$route.query.type
				let tlt = vm.$route.query.productTitle
				let vType = vm.$route.query.voucherType
				vm.type = vm.investType(intype)
				vm.productTitle = vm.reconvert(tlt)
				vm.preInterest = vm.$route.query.preInterest
				vm.amount = vm.$route.query.amount
				vm.voucher = vm.investVoucher(vType)
				vm.voucherUnit = vm.investUnit(vType)
				vm.voucherMoney = vm.$route.query.voucherMoney
			},
			goInvest() {
				if (/(iPhone|iPad|iPod|iOS)/i.test(navigator.userAgent)) {
					this.setupWebViewJavascriptBridge(function (bridge) {
						bridge.callHandler('h5ToNative_GoonInvest', {}, function (response) {
						})
					})
				} else if (/(Android)/i.test(navigator.userAgent)) {
					setTimeout(() => {
						window.TDBridge.h5ToNative_GoonInvest()
					}, 500)
				}
			},
			goInvestRecode() {
				if (/(iPhone|iPad|iPod|iOS)/i.test(navigator.userAgent)) {
					this.setupWebViewJavascriptBridge(function (bridge) {
						bridge.callHandler('h5ToNative_GoonInvestRecode', {
							'tenderId': 666
						}, function (response) {
						})
					})
				} else if (/(Android)/i.test(navigator.userAgent)) {
					setTimeout(() => {
						window.TDBridge.h5ToNative_GoonInvestRecode({'tenderId': 666})
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
			},
			reconvert(str) {
				str = str.replace(/(\\u)(\w{1,4})/gi, function($0) {
					return (String.fromCharCode(parseInt((escape($0).replace(/(%5Cu)(\w{1,4})/g, '$2')), 16)))
				})
				str = str.replace(/(&#x)(\w{1,4});/gi, function($0) {
					return String.fromCharCode(parseInt(escape($0).replace(/(%26%23x)(\w{1,4})(%3B)/g, '$2'), 16))
				})
				str = str.replace(/(&#)(\d{1,6});/gi, function($0) {
					return String.fromCharCode(parseInt(escape($0).replace(/(%26%23)(\d{1,6})(%3B)/g, '$2')))
				})
				return str
			},
			investType(str) {
				switch (str) {
				case '0':
					return '投资成功'
				case '1':
					return '投资成功'
				case '2':
					return '加入成功'
				default: return ''
				}
			},
			investVoucher(str) {
				switch (str) {
				case '0':
					return '-'
				case '1':
					return '抵用券'
				case '2':
					return '加息券'
				default: return ''
				}
			},
			investUnit(str) {
				switch (str) {
				case '0':
					return ' '
				case '1':
					return '元'
				case '2':
					return '%'
				default: return ''
				}
			}
		}
	}
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
	@import "~common/stylus/variable"
	.result
		font-family: 'PingFang-SC-Medium'
		.result_top
			width:100%
			height:2.5rem
			text-align:center
			background-color:$color-background-orange
			overflow:hidden
			i
				font-size:0.8rem
				color:$color-font-f
				font-style:normal
				display:block
				margin-top:0.5rem
			p
				font-size:0.32rem
				color:$color-font-f
				margin-top:0.26rem
		.result_com
			padding:0 0.3rem
			li
				border-bottom:1px solid #e4e4e4
				overflow:hidden
				line-height:0.8rem
				span
					font-size:0.24rem
					color:#b6b5b6
					float:left
				p
					font-size:0.24rem
					color:$color-font-title
					float:right
		.result_bot
			padding:0 0.3rem
			overflow:hidden
			margin-top:0.6rem
			span
				display:inline-block
				font-size:0.3rem
				width:46%
				height:0.8rem
				line-height:0.8rem
				text-align:center
				border-radius:4px
				box-sizing: border-box
			.invest_list
				color:$color-font-f
				background-color:$color-background-orange
				float:left
			.add_list
				color:$color-font-main
				background-color:$color-background
				border:1px solid #b6b5b6
				float:right
				
</style>