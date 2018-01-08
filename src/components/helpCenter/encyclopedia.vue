<template>
	<div class="help">
		<div class="help_bot">
			<ul>
				<li v-for="site in sites">
					<router-link :to="{path:'encyclopediaDetail',query: {id:site.contentId}}">{{ site.title }}<br /><span>{{ site.publishTime }}</span><span class="iconfont">&#xe69b;</span></router-link>
				</li>
			</ul>
		</div>
	</div>
</template>
<script type="text/ecmascript-6">
	export default {
		data () {
			return {
				sites: [],
				accessId: '',
				accessKey: '',
				listUrl: 'api/router/op/selectContentByContentRemark'
			}
		},
		mounted() {
			setTimeout(() => {
				this.getUserInfo()
			}, 500)
		},
		methods: {
			toggle: function (data) {
				this.flag[data] = !this.flag[data]
			},
			getData() {
				let vm = this
				vm.$http({url: vm.listUrl, method: 'POST', params: {'contentRemark': 2, 'pageSize': 1000, 'currentPage': 1}, headers: {accessId: vm.accessId, accessKey: vm.accessKey}, emulateJSON: true})
				.then((res) => {
					let listData = res.data.content.list
					for (var i = 0; i < listData.length; i++) {
						if (listData[i].title.length >= 19) {
							listData[i].title = listData[i].title.substring(0, 17) + '...'
						}
					}
					vm.sites = listData
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
	.help
		max-width:414px
		margin:0 auto
		font-size:0.28rem
		overflow:hidden
		background: #ffffff
		font-family: PingFang-SC-Medium
		ul
			li
				font-size: 0.28rem
				color: #212a36
				line-height: 0.4rem
				padding: 0rem 0.3rem 0rem 0.28rem
				border-bottom: 1px solid #e4e4e4
				a
					width: 100%
					background-color:white
					display:block
					color: #212a36
					padding: 0.15rem 0rem 0.1rem 0rem
					position: relative
					span
						color: #b6b5b6
						font-size: 0.26rem
						&.iconfont
							color: #212a36
							position:absolute
							right:0rem
							top:50%
							margin-top: -0.25rem
							font-size: 0.3rem
</style>