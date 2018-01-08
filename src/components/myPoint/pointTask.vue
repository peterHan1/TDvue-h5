<template>
	<div class="main">
		<div class="task_menu" v-bind:class="bor_bom">
			<span @click="tabCut(1)"><a :class="activeObj1">日常任务</a></span>
			<span @click="tabCut(2)"><a :class="activeObj2" >新手任务</a></span>
		</div>
		<div v-show="new_title" class="explain">在注册30天内完成任务即可获得对应的积分</div>
		<div class="task">
			<div v-if="showValue">
				<ul>
					<li v-for="task in taskNormalData">
						<div class="list_left">
							<span class="sp1">{{ task.taskName }}</span>
							<span class="sp2">{{ task.description }}</span>
						</div>
						<a v-on:click="taskEvent(task.appUrl,task.code,task.todo)" v-bind:class="[errorClass ,task.isComplete=='yes' || task.isOverdue=='yes' ? activeClass : '']">{{ task.todo }}</a>
					</li>
				</ul>
			</div>
			<div v-else>
				<ul>
					<li v-for="task in taskNewData">
						<div class="list_left">
							<span class="sp1">{{ task.taskName }}</span>
							<span class="sp2">{{ task.description }}</span>
						</div>
						<a v-on:click="taskEvent(task.appUrl,task.code,task.todo)" v-bind:class="[errorClass ,task.isComplete=='yes' || task.isOverdue=='yes' ? activeClass : '']">{{ task.todo }}</a>
					</li>
				</ul>
			</div>
		</div>
	</div>
</template>

<script type="text/ecmascript-6">
export default {
	data () {
		return {
			errorClass: '',
			activeClass: 'isOver',
			showValue: true,
			activeObj1: 'on',
			activeObj2: '',
			new_title: false,
			bor_bom: 'bor_bom',
			taskNormalData: [],
			taskNewData: [],
			taskUrl: 'api/router/op/findUserTask',
			accessId: '',
			accessKey: ''
		}
	},
	mounted() {
		setTimeout(() => {
			this.getUserInfo()
		}, 500)
	},
	methods: {
		tabCut (type) {
			let vm = this
			if (type === 1) {
				vm.showValue = true
				vm.activeObj1 = 'on'
				vm.activeObj2 = ''
				vm.new_title = false
				vm.bor_bom = 'bor_bom'
				vm.getData(1)
			} else {
				vm.showValue = false
				vm.activeObj1 = ''
				vm.activeObj2 = 'on'
				vm.new_title = true
				vm.bor_bom = ''
				vm.getData(2)
			}
		},
		getData(type) {
			let vm = this
			vm.$http({url: vm.taskUrl, method: 'POST', params: {'type': type, 'pageSize': 1000}, headers: {accessId: vm.accessId, accessKey: vm.accessKey}, emulateJSON: true})
			.then((res) => {
				let listData = res.data.content.list
				for (var i = 0; i < listData.length; i++) {
					if (listData[i].isOverdue === 'yes') {
						listData[i].todo = '已过期'
					} else if (listData[i].isComplete === 'yes') {
						listData[i].todo = '已完成'
					}
				}
				if (type === 1) {
					vm.taskNormalData = listData
				} else {
					vm.taskNewData = listData
				}
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
						vm.loginStatus = response.status
						vm.getData(1)
					})
				})
			} else if (/(Android)/i.test(navigator.userAgent)) {
				setTimeout(function () {
					window.TDBridge.h5ToNative_GetUserInfo(function(data) {
						data = JSON.parse(data)
						vm.accessId = data.accessId
						vm.accessKey = data.accessKey
						vm.loginStatus = data.status
						vm.getData(1)
					})
				})
			}
		},
		taskEvent(url, code, todo) {
			let vm = this
			if (todo === '已完成' || todo === '已过期') {
				return false
			} else {
				if (code === '5') {
					vm.$router.push({path: '/share'})
				} else if (code === '6') {
					if (/(iPhone|iPad|iPod|iOS)/i.test(navigator.userAgent)) {
						var nowTime1 = new Date().valueOf()
						var ifr = document.createElement('iframe')
						ifr.src = 'tuodao16TD://?id=login' // 打开app的协议,id=login是传参
						ifr.style.display = 'none'
						window.setTimeout(function() {
							var launchTime1 = new Date().valueOf() - nowTime1
							if (launchTime1 < 28) {
								// 下载app的地址
								document.body.removeChild(ifr)
								window.location.href = 'itms-apps://itunes.apple.com/app/id1030238074'
							}
						}, 25)
						document.body.appendChild(ifr)
					} else if (navigator.userAgent.match(/android/i)) {
						var nowTime2 = new Date().valueOf()
						window.setTimeout(function() {
							var launchTime2 = new Date().valueOf() - nowTime2
							if (launchTime2 < 28) {
								// 下载app的地址
								window.location.href = 'itms-apps://itunes.apple.com/app/id1030238074'
							}
						}, 25)
						window.location.href = 'tuodao16TD://'
					}
				} else {
					if (/(iPhone|iPad|iPod|iOS)/i.test(navigator.userAgent)) {
						vm.setupWebViewJavascriptBridge(function (bridge) {
							bridge.callHandler(url, {}, function (response) {})
						})
					} else if (/(Android)/i.test(navigator.userAgent)) {
						if (url === 'h5ToNative_Login') {
							window.TDBridge.h5ToNative_Login(function(data) {})
						} else if (url === 'h5ToNative_ShowOpenDepositAlert') {
							window.TDBridge.h5ToNative_ShowOpenDepositAlert(function(data) {})
						} else if (url === 'h5ToNative_GoInvestList') {
							window.TDBridge.h5ToNative_GoInvestList(function(data) {})
						} else if (url === 'h5ToNative_GoRecharge') {
							window.TDBridge.h5ToNative_GoRecharge(function(data) {})
						} else if (url === 'h5ToNative_AttentionWeChat') {
							window.TDBridge.h5ToNative_AttentionWeChat(function(data) {})
						} else if (url === 'h5ToNative_AutoTenderSet') {
							window.TDBridge.h5ToNative_AutoTenderSet(function(data) {})
						} else if (url === 'h5ToNative_CreditsExchange') {
							window.TDBridge.h5ToNative_CreditsExchange(function(data) {})
						} else if (url === 'h5ToNative_find') {
							window.TDBridge.h5ToNative_find(function(data) {})
						}
					}
				}
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
		max-width: 414px
		margin:auto
		overflow:hidden
		background: #ffffff
		font-family: PingFang-SC-Medium
		font-size:0.24rem
		.bor_bom
			border-bottom:0.2rem solid #f2f3f7
		.task_menu
			width: 100%
			height:0.8rem
			span
				display: inline-block
				text-align: center
				width: 49%
				padding-top: 0.28rem
			a
				display:inline-block
				padding-bottom:0.2rem
				font-size:0.28rem
				&.on
					color:$color-font-orange
					border-bottom:0.04rem solid $color-font-orange
		.explain
			font-size:0.2rem
			color:#bd8147
			text-align:center
			height:0.6rem
			line-height:0.6rem
			background:#fff7e0
		ul
			width:100%
			li
				overflow:hidden
				border-bottom: 1px solid #e4e4e4
				font-size: 0.24rem
				height: 1.5rem
				padding-left: 0.3rem
				.list_left
					margin-top: 0.45rem
					float:left
					span
						display: block
				.sp1
					margin-bottom: 0.2rem
					font-size: 0.28rem
					color: #212a36
				.sp2
					color: #b6b5b6
				a
					display: block
					float: right
					margin-right: 0.3rem
					width: 1.2rem
					height: 0.5rem
					line-height: 0.5rem
					-webkit-border-radius:0.04rem
					-moz-border-radius:0.04rem
					border-radius:0.04rem
					text-align: center
					color: #ffffff
					background:$color-font-orange
					margin-top:0.5rem
					&.isOver
							background: #b6b5b6
</style>