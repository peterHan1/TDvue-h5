<template>
	<ul class="main">
		<li class="title"><span>{{ title }}</span><span class="right">{{ publishTime }}</span></li>
		<li class="content">{{ content }}</li>
	</ul>
</template>
<script type="text/ecmascript-6">
	export default {
		data () {
			return {
				title: '',
				publishTime: '',
				content: '',
				accessId: '',
				accessKey: '',
				detailUrl: 'api/router/op/selectContentByContentId'
			}
		},
		mounted() {
			setTimeout(() => {
				this.getUserInfo()
			}, 500)
		},
		methods: {
			getData() {
				let vm = this
				// vm.$route.query.id
				vm.$http({url: vm.detailUrl, method: 'POST', params: {'contentId': vm.$route.query.id}, headers: {accessId: vm.accessId, accessKey: vm.accessKey}, emulateJSON: true})
				.then((res) => {
					let listData = res.data.content
					vm.title = listData.title
					vm.publishTime = listData.publishTime
					vm.content = listData.content
				}, (res) => {
					console.log('请求失败！')
				})
			},
			getUserInfo() {
				let vm = this
				if (/(iPhone|iPad|iPod|iOS)/i.test(navigator.userAgent)) {
					vm.setupWebViewJavascriptBridge(function (bridge) {
						bridge.callHandler('h5ToNative_GetUserInfo', {}, function (response) {
							vm.accessId = response.accessId
							vm.accessKey = response.accessKey
							vm.getData()
						})
					})
				} else if (/(Android)/i.test(navigator.userAgent)) {
					setTimeout(function () {
						window.TDBridge.h5ToNative_GetUserInfo(function(data) {
							data = JSON.parse(data)
							vm.accessId = data.accessId
							vm.accessKey = data.accessKey
							vm.getData()
						})
					})
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
	.main
		max-width:414px
		margin:0 auto
		font-size:0.24rem
		overflow:hidden
		background: #ffffff
		font-family: PingFang-SC-Medium
		li
			padding: 0rem 0.3rem
			&.title
				font-size: 0.28rem
				color: #212a36
				height: 0.8rem
				line-height: 0.8rem
				border-bottom: 1px solid #e4e4e4
				.right
					font-size:0.26rem
					color: #b6b5b6
					float: right
			&.content
				color:#626262
				font-size:0.24rem
				line-height: 0.3rem
				padding: 0.3rem 0.3rem
</style>