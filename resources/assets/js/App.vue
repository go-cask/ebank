<template>
	<div>
		<header class="mdui-appbar mdui-appbar-fixed">
			<div class="mdui-toolbar mdui-color-theme">
				<span class="mdui-btn mdui-btn-icon mdui-ripple mdui-ripple-white" mdui-drawer="{target: '#main-drawer', swipe: true}"><i class="mdui-icon material-icons">menu</i></span>
				<router-link :to="{path:'/'}" class="mdui-typo-headline mdui-hidden-md-up">EBank</router-link>
				<router-link :to="{path:'/'}" class="mdui-typo-title mdui-hidden-sm-down">EBank 电子银行</router-link>
				<div class="mdui-toolbar-spacer"></div>
				<!--<div class="mdui-spinner mdui-spinner-colorful mdui-hidden ajax_loading"></div>-->
				<span class="mdui-btn mdui-ripple" mdui-menu="{target: '.menu_user'}">
					<i class="mdui-icon material-icons">person</i>{{user.name || '未登录'}}
				</span>
				<ul class="mdui-menu mdui-menu-cascade menu_user">
					<li class="mdui-menu-item">
						<a href="javascript:;" class="mdui-ripple">
							<i class="mdui-menu-item-icon mdui-icon material-icons">person</i>{{user.realname || '无姓名'}}
						</a>
					</li>
					<li class="mdui-menu-item">
						<a href="javascript:;" class="mdui-ripple">
							<i class="mdui-menu-item-icon mdui-icon material-icons">phone_iphone</i>{{user.mobile || '无手机号'}}
						</a>
					</li>
					<li class="mdui-menu-item">
						<a href="javascript:;" class="mdui-ripple">
							<i class="mdui-menu-item-icon mdui-icon material-icons">email</i>{{user.email || '无邮箱'}}
						</a>
					</li>
					<div class="mdui-divider"></div>
					<li class="mdui-menu-item">
						<a href="javascript:;" class="mdui-ripple">
							<i class="mdui-menu-item-icon mdui-icon material-icons">last_page</i>本次登录
						</a>
					</li>
					<li class="mdui-menu-item">
						<a href="javascript:;" class="mdui-ripple">
							<i class="mdui-menu-item-icon"></i>{{user.last_login || '无历史登录'}}
						</a>
					</li>
					<div class="mdui-divider"></div>
					<li class="mdui-menu-item">
						<a href="javascript:;" class="mdui-ripple" @click="password_reset_dialog.open()">
							<i class="mdui-menu-item-icon mdui-icon material-icons">vpn_key</i>修改密码
						</a>
					</li>
				</ul>
				<a class="mdui-btn" v-if="user" @click="logout">
					注销
				</a>
			</div>
		</header>
		<div class="mdui-drawer mdui-color-white" id="main-drawer">
			<div class="mdui-card">
				<div class="mdui-card-media">
					<img src="../images/snow_square_tiny.png" style="height:190px;" />
					<!-- 卡片中可以包含一个或多个菜单按钮 -->
					<div class="mdui-card-menu">
						<a target="_blank" href="//github.com/yybawang/laravel-ebank">
							<button class="mdui-btn mdui-text-color-grey">github</button>
						</a>
					</div>
					<div class="mdui-card-media-covered mdui-card-media-covered-transparent">
						<div class="mdui-card-primary">
							<!--<img class="mdui-img-circle" src="../images/avatar.png" style="width:45px;" />-->
							<!--<a class="mdui-btn mdui-btn-icon mdui-ripple"><i class="mdui-icon material-icons">person</i></a>-->
							<div class="mdui-card-primary-title mdui-text-uppercase">{{user.name}}</div>
							<div class="mdui-card-primary-subtitle">{{user.realname}}</div>
						</div>
					</div>
				</div>
			</div>
			<div class="mdui-list" mdui-collapse="{accordion: true}">
				<div v-for="(val,key,index) in menu" :class="{'mdui-collapse-item':true,'mdui-collapse-item-open':index == 0 ? true :false}">
					<div class="mdui-collapse-item-header mdui-list-item mdui-ripple">
						<i class="mdui-list-item-icon mdui-icon material-icons mdui-text-color-grey">menu</i>
						<div class="mdui-list-item-content" v-text="key"></div>
						<i class="mdui-collapse-item-arrow mdui-icon material-icons">keyboard_arrow_down</i>
					</div>
					<div class="mdui-collapse-item-body mdui-list">
						<div v-for="(val2,key2,index2) in val" @click="menu_active">
						<router-link :to="{path:val2}" class="mdui-list-item mdui-ripple">{{key2}}</router-link>
						</div>
					</div>
				</div>
			</div>
		</div>
		
		<!--进度条，用于加载提示-->
		<div class="mdui-progress">
			<div class="mdui-progress-indeterminate mdui-hidden ajax_loading"></div>
		</div>
		<!--<div class="mdui-container">-->
		<div class="mdui-p-a-3">
			<div id="app">
				<router-view @init="init" @initClear="initClear"></router-view> <!-- 路由引入的组件将在这里被渲染 -->
			</div>
		</div>
		
		<!--修改密码弹窗-->
		<div class="mdui-dialog password_reset_dialog">
			<div class="mdui-dialog-title">
				修改登录密码
			</div>
			<div class="mdui-dialog-content">
				<div class="mdui-container">
					<div class="mdui-textfield">
						<label class="mdui-textfield-label">现登录密码</label>
						<input class="mdui-textfield-input" type="password" v-model="password_reset.password_old" />
					</div>
				</div>
				<div class="mdui-container">
					<div class="mdui-textfield">
						<label class="mdui-textfield-label">新登录密码</label>
						<input class="mdui-textfield-input" type="password" v-model="password_reset.password_new" />
					</div>
				</div>
				<div class="mdui-container">
					<div class="mdui-textfield">
						<label class="mdui-textfield-label">重复新登录密码</label>
						<input class="mdui-textfield-input" type="password" v-model="password_reset.password_new_confirmation" />
					</div>
				</div>
			</div>
			<div class="mdui-dialog-actions">
				<a class="mdui-btn mdui-ripple" mdui-dialog-close>取消</a>
				<a class="mdui-btn mdui-ripple mdui-color-theme" @click="password_reset_submit">提交</a>
			</div>
		</div>
	</div>
</template>
<script>
	export default {
		data(){
			return {
				user : '',
				menu : '',
				initing : false,
				password_reset_dialog : '',
				password_reset : {
					password_old : '',
					password_new : '',
					password_new_confirmation : '',
				}
			}
		},
		methods : {
			menu_active(t){
				$('.mdui-list-item-actived').removeClass('mdui-list-item-actived mdui-color-theme');
				$(t.currentTarget).addClass('mdui-list-item-actived mdui-color-theme');
			},
			password_reset_submit(){
				let t = this;
				post('/rule/password_reset',this.password_reset,function(data){
					t.password_reset_dialog.close();
					mdui.alert('已成功修改，下次登录请使用新密码','修改登录密码完成',function(){},{history:false})
					for(let i in t.password_reset){
						t.password_reset[i] = '';
					}
				})
			},
			init(){
				let t = this;
				// if(!t.initing){
					t.initing = true;
					get('/init',{},function(data){
						t.user = data.user;
						t.menu = data.menu;
						t.initing = false;
					});
				// }
			},
			initClear(){
				let t = this;
				t.menu = '';
				t.user = '';
			},
			logout(){
				let t = this;
				tips('即将跳转到登录页');
				get('/logout',{},function(data){
					t.$router.push({path:'/login'});
				});
			}
		},
		mounted(){
			let t = this;
			t.password_reset_dialog = new mdui.Dialog('.password_reset_dialog',{history:false});
			t.init();
		}
	}
</script>
