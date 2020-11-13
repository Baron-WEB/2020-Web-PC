<template>
  <div id="app-content">
    <section class="page-header page-header-xs">
      <div class="container">
        <h1>个人中心</h1>

        <el-breadcrumb separator-class="el-icon-arrow-right">
          <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
          <el-breadcrumb-item>个人中心</el-breadcrumb-item>
          <el-breadcrumb-item>个人信息设置</el-breadcrumb-item>
        </el-breadcrumb>
        <!-- /breadcrumbs -->

        <!-- page tabs -->
        <ul class="page-header-tabs list-inline">

          <li>
            <a @click="goto('/account/order/list','OrderList')">我的订单</a>
          </li>
          <li>
            <a @click="goto('/account/contact/list','ContactList')">常用联系人</a>
          </li>
          <li>
            <a @click="goto('/account/wishlist','WishList')">我的收藏</a>
          </li>
          <li class="active">
            <a>个人设置</a>
          </li>
          <li>
            <a @click="goto('/account/ewallet','EWallet')">充值</a>
          </li>
          <li>
            <a @click="goto('/account/payment','Payment')">未支付订单</a>
          </li>
          <li>
            <a @click="goto('/account/ivoiceControl','IvoiceControl')">发票抬头管理</a>
          </li>
        </ul>
        <!-- /page tabs -->
      </div>
    </section>
    <!-- <section v-if="user!=null">
      <div class="container">
        <div class="panel panel-default">
          <div class="panel-heading">
            <h2 class="panel-title">个人信息设置</h2>
          </div>
          <fieldset class="padding-40">
            <div class="row">
              <h3 class="text-center">个人信息</h3>
              <div class="col-md-6 col-sm-6">
                <label for="nickName">昵称</label>
                <el-input
                  id="nickName"
                  name="nickName"
                  type="text"
                  v-bind:class="{error:tips.nicknameTip}"
                  v-model="user.nickname"
                />
                <label class="error" for="nickName">{{tips.nicknameTip}}</label>
              </div>

              <div class="col-md-6 col-sm-6">
                <label for="email">Email</label>
                <el-input
                  id="email"
                  name="email"
                  type="text"
                  v-bind:class="{error:tips.emailTip}"
                  v-model="user.email"
                />
                <label class="error" for="email">{{tips.emailTip}}</label>
              </div>
            </div>
            <el-button
              @click="saveBasic"
              type="primary"
              class="pull-right margin-top-20"
              icon="el-icon-edit"
            >保存</el-button>
          </fieldset>

          <hr />
          <h3 class="text-center">修改密码</h3>
          <fieldset class="padding-40">
            <div class="row">
              <form id="userPwdFrom" onsubmit="return false;">
                <div class="col-md-12 col-sm-12">
                  <label for="oldpassword">输入当前密码</label>
                  <el-input
                    id="oldpassword"
                    name="oldpassword"
                    type="password"
                    v-bind:class="{error:tips.oldpwdTip}"
                    v-model="pwds.oldpwd"
                  />
                  <label class="error" for="oldpassword">{{tips.oldpwdTip}}</label>
                </div>

                <div class="col-md-6 col-sm-6">
                  <label for="password">新的密码</label>
                  <el-input
                    id="password"
                    name="password"
                    type="password"
                    v-bind:class="{error:tips.pwdTip}"
                    v-model="pwds.pwd"
                  />
                  <label class="error" for="password">{{tips.pwdTip}}</label>
                </div>

                <div class="col-md-6 col-sm-6">
                  <label for="rpassword">确认密码</label>
                  <el-input
                    id="rpassword"
                    name="rpassword"
                    type="password"
                    v-bind:class="{error:tips.rpwdTip}"
                    v-model="pwds.rpwd"
                  />
                  <label class="error" for="rpassword">{{tips.rpwdTip}}</label>
                </div>
              </form>
            </div>
            <el-button
              @click="savePwd"
              type="primary"
              class="pull-right margin-top-20"
              icon="el-icon-edit"
            >保存</el-button>
          </fieldset>
        </div>
      </div>
    </section> -->
     <section v-if="user!=null">
        <div class="container">
          <div class="panel panel-info">
            <div class="panel-heading">
                <h3 class="panel-title">个人信息设置</h3>
            </div>
            <div class="panel-body">
                <div class="row" style="padding: 0 20px">
                  <h3 class="text-center">个人信息</h3>
                  <div class="col-md-6 col-sm-6">
                    <label for="email">昵称</label>
                    <el-input type="text" name="nickname" id="nickname" v-model="user.nickname"/>
                    <label class="error" for="nickName">{{tips.nicknameTip}}</label>
                  </div>
                  <div class="col-md-6 col-sm-6">
                    <label for="email">Email</label>
                    <el-input type="text" id="email" name="email" v-model="user.email"/>
                    <label class="error" for="email">{{tips.emailTip}}</label>
                  </div>
                    <el-button type="primary" round class="pull-right margin-top-20" @click="saveBasic">保存</el-button>
                </div>
            </div>
          </div>
        </div>
     </section>





  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      // user: {
      //   nickname: '11',
      //   email: '11'
      // },
      user: null,
      pwds: {
        oldpwd: "",
        pwd: "",
        rpwd: ""
      },
      tips: {
        nicknameTip: null,
        emailTip: null,
        oldpwdTip: null,
        pwdTip: null,
        rpwdTip: null
      }
    };
  },
  created() {
    this.getUserBasic();
  },
  methods: {
    goto(path, name, val) {
      this.$router.push({
        path: path,
        name: name
      });
    },
    getUserBasic() {
      var vm = this;
      var userId = vm.Storage.Session.get("User").id;
      if (userId) {
        vm.$post(vm.API.API_URL_CUSTOMER_BASIC, {
          id: userId
        }).then(res => {
          console.log(res);
          if (res.errorCode == 0 && res.data != null) {
            vm.user = res.data;
          }
        });
      }
    },
    saveBasic() {
      var vm = this;
      var email = /^([0-9A-Za-z\-_\.]+)@([0-9a-z]+\.[a-z]{2,3}(\.[a-z]{2})?)$/g;
      if (vm.user.nickname.length == 0 && vm.user.email.length == 0) {
        vm.tips.nicknameTip = "昵称不能为空!";
        vm.tips.emailTip = "邮箱不能为空!";
      } else if (vm.user.nickname.length > 20) {
        vm.tips.nicknameTip = "昵称不能超过20位!";
        vm.tips.emailTip = null;
      } else if (vm.user.nickname.length == 0) {
        vm.tips.nicknameTip = "昵称不能为空!";
        vm.tips.emailTip = null;
      } else if (vm.user.email.length > 50) {
        vm.tips.emailTip = "邮箱不能超过50位!";
        vm.tips.nicknameTip = null;
      } else if (!email.test(vm.user.email)) {
        vm.tips.emailTip = "邮箱格式不正确!";
        vm.tips.nicknameTip = null;
      } else if (vm.user.email.length == 0) {
        vm.tips.emailTip = "邮箱不能为空!";
        vm.tips.nicknameTip = null;
      } else {
        vm.tips.emailTip = null;
        vm.tips.nicknameTip = null;
        var user = vm.Storage.Session.get("User");
        if (user) {
          var userId = user.id;
          vm.$post(vm.API.API_URL_UPDATE_CUSTOMER, {
            id: userId,
            nickname: vm.user.nickname,
            email: vm.user.email
          }).then(res => {
            if (res.errorCode == 0 && res.data != null) {
              if (res.data) {
                vm.Toastr.success("修改成功！");
                var user = {
                  id: userId,
                  userName: vm.user.userName,
                  nickname: vm.user.nickname
                };
                console.log(user);
                vm.Storage.Session.set("User", user);
                vm.$store.commit("setUser", user);
              } else {
                vm.Toastr.warning("修改失败！");
              }
            }
          });
        }
      }
    },

    saveBasic1() {
      var vm = this;
      // email正则表达式
      var email = /(^[0-9A-Za-z\-_\.]+)@([0-9a-z]+\.[a-z]{2,3}(\.[a-z]{2})?)$/g
      if(vm.user.nickname.length == 0 && vm.user.email.length ==0) {
        vm.tips.nicknameTip = "昵称不能为空"
        vm.tips.emailTip = "邮箱不能为空"
      } else if(vm.user.nickname.length == 0) {
          vm.tips.nicknameTip = "昵称不能为空"
          vm.tips.emailTip = null
      } else if(vm.user.nickname.length>20) {
        vm.tips.nicknameTip = '昵称不能超过20位'
        vm.tips.emailTip = null
      } else if(vm.user.email.length == 0) {
        vm.tips.emailTip = '邮箱不能为空'
        vm.tips.nicknameTip = null
      } else if(!email.test(vm.user.email)) {
        vm.tips.emailTip = '邮箱格式不正确'
        vm.tips.nicknameTip = null
      } else {
          vm.tips.emailTip = null
          vm.tips.nicknameTip = null
          var user = this.Storage.Session.get('User')
          if(user) {
            var userId = user.id
           vm.$post(this.API.API_URL_UPDATE_CUSTOMER, {
             id: userId,
             nickName: vm.user.nickname,
             email: vm.user.email
           }).then(res=> {
             console.log(res);
             if(res.errorCode == 0 && res.data !=null) {
               if(res.data) {
                 vm.Toastr.success('修改成功!')
                 var user = {
                   id: userId,
                   nickname: vm.user.nickname,
                   username: vm.user.userName
                 }
                 console.log(user);
                 vm.Storage.Session.set('User',user)
                 vm.$store.commit("setUser", user);
               } else {
                 vm.Toastr.warning('修改失败!')
               }
             }
           })
          }
      }
      },
    savePwd() {
      var vm = this;
      if (
        vm.pwds.oldpwd.length == 0 &&
        vm.pwds.pwd.length == 0 &&
        vm.pwds.rpwd.length == 0
      ) {
        vm.tips.oldpwdTip = "请输入原始密码！";
        vm.tips.pwdTip = "请输入新密码！";
        vm.tips.rpwdTip = "请输入确认密码！";
      } else if (vm.pwds.oldpwd.length == 0) {
        vm.tips.oldpwdTip = "请输入原始密码！";
        vm.tips.pwdTip = null;
        vm.tips.rpwdTip = null;
      } else if (vm.pwds.oldpwd.length > 20) {
        vm.tips.oldpwdTip = "密码不能超过20位！";
        vm.tips.pwdTip = null;
        vm.tips.rpwdTip = null;
      } else if (vm.pwds.pwd.length == 0) {
        vm.tips.oldpwdTip = null;
        vm.tips.pwdTip = "请输入新密码！";
        vm.tips.rpwdTip = null;
      } else if (vm.pwds.pwd.length > 20) {
        vm.tips.oldpwdTip = null;
        vm.tips.pwdTip = "密码不能超过20位！";
        vm.tips.rpwdTip = null;
      } else if (vm.pwds.rpwd.length == 0) {
        vm.tips.oldpwdTip = null;
        vm.tips.pwdTip = null;
        vm.tips.rpwdTip = "请输入确认密码！";
      } else if (vm.pwds.rpwd.length > 20) {
        vm.tips.oldpwdTip = null;
        vm.tips.pwdTip = null;
        vm.tips.rpwdTip = "密码不能超过20位！";
      } else if (vm.pwds.rpwd.length != vm.pwds.pwd.length) {
        vm.tips.oldpwdTip = null;
        vm.tips.pwdTip = null;
        vm.tips.rpwdTip = "密码与确认密码不一致！";
      } else {
        vm.tips.oldpwdTip = null;
        vm.tips.pwdTip = null;
        vm.tips.rpwdTip = null;
        var user = vm.Storage.Session.get("User");
        if (user) {
          var userId = user.id;
          vm.$post(vm.API.API_URL_USER_CHANGE_PASSWD, {
            userId: userId,
            oldpassword: vm.pwds.oldpwd,
            rpassword: vm.pwds.rpwd
          }).then(res => {
            console.log(res);
            if (res.errorCode == 0 && res.data) {
              vm.Toastr.success("密码修改成功, 2秒后自动退回登录页面!");
              vm.pwds.oldpwd = "";
              vm.pwds.pwd = "";
              vm.pwds.rpwd = "";
              setTimeout(() => {
                vm.logout();
              }, 2000);
            } else {
              vm.Toastr.error("原密码不正确！");
            }
          });
        }
      }
    },
    logout() {
      this.user = null;
      this.Storage.Session.remove("User");
      this.cartCount = "";
      if (this.$route.path.indexOf("/account") != -1) {
        this.$router.push({
          path: "/login",
          name: "Login",
          params: {
            msg: "您还未登录，请登录！"
          }
        });
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.error {
  color: red;
}
</style>
