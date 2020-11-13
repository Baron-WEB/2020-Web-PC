<template>
  <div id="app-content">
    <section class="page-header page-header-xs">
      <div class="container">
        <h1>个人中心</h1>

        <!-- breadcrumbs -->
        <el-breadcrumb separator-class="el-icon-arrow-right">
          <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
          <el-breadcrumb-item>个人中心</el-breadcrumb-item>
          <el-breadcrumb-item>常用联系人</el-breadcrumb-item>
        </el-breadcrumb>
        <!-- /breadcrumbs -->

        <!-- page tabs -->
        <ul class="page-header-tabs list-inline">
          <li>
            <a @click="goto('/account/order/list','OrderList')">我的订单</a>
          </li>
          <li class="active">
            <a>常用联系人</a>
          </li>
          <li>
            <a @click="goto('/account/wishlist','WishList')">我的收藏</a>
          </li>
          <li>
            <a @click="goto('/account/setting','UserSetting')">个人设置</a>
          </li>
          <li>
            <a @click="goto('/account/ewallet','EWallet')">充值</a>
          </li>
          <li>
            <a  @click="goto('/account/payment','Payment')">未支付订单</a>
          </li>
        </ul>
        <!-- /page tabs -->
      </div>
    </section>
    <section>
      <div class="container">
        <div class="panel panel-default">
          <div class="panel-heading">
            <h2 class="panel-title">常用联系人</h2>
          </div>

          <el-table :data="accountList.data" style="width: 100%">
            <el-table-column
              label="默认联系人"
              width="220">
              <template slot-scope="scope" >
                <el-radio v-model="radio"  name="contect"
                      checked="checked"
                      @click.native.prevent="doOptions(API.API_URL_CONTECT_DEFAULT,scope.row.id,'设置成功' )" >
                </el-radio>
      
              </template>
            </el-table-column>
            <el-table-column
              label="联系人姓名"
              width="220">
              <template slot-scope="scope">
                <span style="margin-left: 10px">{{  scope.row.name}}</span>
              </template>
            </el-table-column>
            <el-table-column
              label="联系人电话"
              width="220">
              <template slot-scope="scope">
                <span style="margin-left: 10px">{{ scope.row.mobile }}</span>
              </template>
            </el-table-column>
            <el-table-column
              label="联系人地址"
              width="220">
              <template slot-scope="scope">
                <span style="margin-left: 10px">{{  scope.row.address}}</span>
              </template>
            </el-table-column>
            <el-table-column
              label="操作"
              width="220">
               <template slot-scope="scope">
        <el-button
          size="mini"
          @click="changeById(scope.row.id,scope.row.name,scope.row.mobile,scope.row.address)">编辑</el-button>
        <el-button
          size="mini"
          type="danger"
          @click="doOptions(API.API_URL_CONTECT_REMOVE,scope.row.id,'删除成功')">删除</el-button>
      </template>
            </el-table-column>
            
         </el-table>
         
          <div class="panel-body">
            <fieldset class="margin-top-10">
              <div class="row">
                <div class="col-md-6 col-sm-6">
                  <input type="hidden" name="id">
                  <label for="billing_firstname">
                    姓名&nbsp;
                    <span class="required">*</span>
                  </label>
                  <el-input id="billing_firstname" v-model="name" type="text" maxlength="50"/>
                  <div class="erro">{{tips.nameMsg}}</div>
                </div>
                <div class="col-md-6 col-sm-6">
                  <label for="billing_lastname">
                    电话&nbsp;
                    <span class="required">*</span>
                  </label>
                  <el-input id="billing_lastname" v-model="mobile" type="text" maxlength="11"/>
                  <div class="erro">{{tips.mobileMsg}}</div>
                </div>
              </div>
              <div class="row">
                <div class="col-lg-12">
                  <label for="billing_address1">
                    地址&nbsp;
                    <span class="required">*</span>
                  </label>
                  <el-input
                    id="billing_address1"
                    v-model="address"
                    type="text"
                    placeholder
                    maxlength="255"
                  />
                  <div class="erro">{{tips.addressMsg}}</div>
                </div>
              </div>
              <el-button
                @click="addContact"
                type="primary"
                class="pull-right margin-top-20"
                icon="el-icon-edit"
              >保存联系人</el-button>
            </fieldset>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: "concatList",
  data() {
    return {
      radio:"",
      accountList: null,
      name: null,
      mobile: null,
      address: null,
      editFlag: false,
      editId: null,
      tips: {
        nameMsg: "",
        mobileMsg: "",
        addressMsg: ""
      },
      // tableData:[
      //   {
      //       date: '2016-05-02',
      //       name: '王小虎',
      //       address: '上海市普陀区金沙江路 1518 弄'
      //     }
      // ]
    };
  },
  created() {
    this.getAccountList();
  },
  methods: {
    getAccountList() {
      var vm = this;
      var user = vm.Storage.Session.get("User");
      if (user) {
        var userId = user.id;
        vm.$post(vm.API.API_URL_CONTECT, 
        { userId: userId }
        ).then(res => {
          if (res.errorCode == 0 && res.data != null) {
            vm.accountList = res.data;
          }
        });
      }
    },
    changeById(id, name, mobile, address) {
      this.editId = id;
      this.name = name;
      this.mobile = mobile;
      this.address = address;
      this.editFlag = true;
    },
    addContact() {
      var url = this.API.API_URL_CONTECT_ADD;
      var msg1 = "添加成功!";
      var msg2 = "联系人至多添加5人!";
      if (this.editFlag) {
        url = this.API.API_URL_CONTECT_UPDATE;
        msg1 = "修改成功!";
        msg2 = "修改失败!";
      }
      var mobile = /^(13[0-9]{9})|(18[0-9]{9})|(14[0-9]{9})|(17[0-9]{9})|(15[0-9]{9})$/;
      var vm = this;
      if (!vm.name && !vm.mobile && !vm.address) {
        vm.tips.nameMsg = "请输入用户名";
        vm.tips.mobileMsg = "请输入手机号码";
        vm.tips.addressMsg = "请输入地址";
      } else if (!vm.name) {
        vm.tips.mobileMsg = "";
        vm.tips.addressMsg = "";
        vm.tips.nameMsg = "请输入用户名";
      } else if (!vm.mobile) {
        vm.tips.nameMsg = "";
        vm.tips.addressMsg = "";
        vm.tips.mobileMsg = "请输入手机号码";
      } else if (!mobile.test(vm.mobile) || vm.mobile.length != 11) {
        vm.tips.nameMsg = "";
        vm.tips.addressMsg = "";
        vm.tips.mobileMsg = "请输入正确的手机号码";
      } else if (!vm.address) {
        vm.tips.nameMsg = "";
        vm.tips.mobileMsg = "";
        vm.tips.addressMsg = "请输入地址";
      } else {
        vm.tips.nameMsg = "";
        vm.tips.mobileMsg = "";
        vm.tips.addressMsg = "";
        var user = vm.Storage.Session.get("User");
        if (user) {
          var userId = user.id;
          vm.$post(url, {
            id: vm.editId,
            userId: userId,
            name: vm.name,
            mobile: vm.mobile,
            address: vm.address
          }).then(res => {
            if (res.errorCode == 0) {
              if (res.data) {
                vm.getAccountList();
                vm.name = "";
                vm.mobile = "";
                vm.address = "";
                vm.Toastr.success(msg1);
                if (vm.editFlag) {
                  vm.editId = null;
                  vm.editFlag = false;
                }
              } else {
                vm.Toastr.error(msg2);
              }
            }
          });
        }
      }
    },
    doOptions(path, id, msg) {
      var vm = this;
      var user = vm.Storage.Session.get("User");
      if (user) {
        var userId = user.id;
        if (msg == "删除成功" && vm.editFlag && vm.editId == id) {
          vm.Toastr.error("联系人编辑中,不允许删除");
        } else {
          if (userId && id) {
            vm.$post(path, {
              userId: userId,
              id: id
            }).then(res => {
              if (res.errorCode == 0 && res.data) {
                vm.Toastr.success(msg);
                vm.getAccountList();
              }
            });
          }
        }
      }
    },
    goto(path, name, val) {
      this.$router.push({
        path: path,
        name: name
      });
    }
  }
};
</script>

<style scoped>
.contact {
  margin-top: 0px !important;
  border-bottom: 1px solid #ddd;
}
.erro,
.required {
  color: red;
}
</style>
