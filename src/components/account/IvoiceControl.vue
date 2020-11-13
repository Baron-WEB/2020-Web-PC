<template>
  <div id="app-content">
    <section class="page-header page-header-xs">
      <div class="container">
        <h1>个人中心</h1>
        <!-- breadcrumbs -->
        <el-breadcrumb separator-class="el-icon-arrow-right">
          <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
          <el-breadcrumb-item>个人中心</el-breadcrumb-item>
          <el-breadcrumb-item>发票抬头管理</el-breadcrumb-item>
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
          <li>
            <a @click="goto('/account/setting','UserSetting')">个人设置</a>
          </li>
          <li>
            <a @click="goto('/account/ewallet','EWallet')">充值</a>
          </li>
          <li>
            <a  @click="goto('/account/payment','Payment')">未支付订单</a>
          </li>
          <li>
            <a  @click="goto('/account/footPrint','FootPrint')">浏览足迹</a>
          </li>
          <li class="active">
            <a>发票抬头管理</a>
          </li>
        </ul>
        <!-- /page tabs -->
      </div>
    </section>
    <section>
      <div class="container">
        <div class="panel panel-default">
          <div class="panel-heading">
            <h2 class="panel-title">发票抬头管理</h2>
          </div>

     <!--     <table class="table contact">
            <thead>
              <tr>
                <th>默认发票抬头</th>
                <th>增值税专用发票/普通发票</th>
                <th>单位名称</th>
                <th>纳税人识别码</th>
                <th>开户银行</th>
                <th>银行账号</th>
                <th>企业地址</th>
                <th>企业电话</th>
                <th>操作</th>
              </tr>
            </thead>
            <tbody>
              <template v-if="accountList!=null&&accountList.data.legnth!=0">
                <tr v-for="(z,index) in accountList.data">
                  <td>
                     &nbsp;{{index+1}}.
                  </td>
                  <td>{{z.type == 1 ? type1 : type2}}</td>
                  <td>{{z.receiving_name}}</td>
                  <td>{{z.consignor_id}}</td>
                  <td>{{z.opening_bank}}</td>
                  <td>{{z.bank_account}}</td>
                  <td>{{z.business_address}}</td>
                  <td>{{z.enterprise_phone}}</td>
                  <td>
                    <a @click="changeById(
                    z.id,
                    z.type,
                    z.receiving_name,
                    z.consignor_id,
                    z.opening_bank,
                    z.bank_account,
                    z.business_address,
                    z.enterprise_phone)">编辑</a>
                    <a @click="centerDialogVisible = true">删除</a>
                  </td>
                  <el-dialog
                    title="提示"
                    :visible.sync="centerDialogVisible"
                    width="30%"
                    center>
                    <span>确认删除此发票抬头？</span>
                    <span slot="footer" class="dialog-footer">
                      <el-button @click="centerDialogVisible = false">取 消</el-button>
                      <el-button type="danger"  @click="doOptions(API.API_URL_CONSIGNOR_REMOVE,z.id,'删除成功');centerDialogVisible = false">删 除</el-button>
                    </span>
                  </el-dialog>
                </tr>
              </template>
            </tbody>
          </table>-->


          <el-table :data="accountList.data" style="width: 100%">
            <el-table-column
              label="默认发票抬头"
              width="140">
              <template slot-scope="scope">
                <span style="margin-left: 10px">{{scope.$index+1}}</span>
              </template>
            </el-table-column>
            <el-table-column
              label="增值税专用发票/普通发票"
              width="200">
              <template slot-scope="scope">
                <span style="margin-left: 10px">{{  scope.row.type == 1 ? type1:type2}}</span>
              </template>
            </el-table-column>
            <el-table-column
              label="单位名称"
              width="140">
              <template slot-scope="scope">
                <span style="margin-left: 10px">{{ scope.row.receiving_name }}</span>
              </template>
            </el-table-column>
            <el-table-column
              label="纳税人识别码"
              width="110">
              <template slot-scope="scope">
                <span style="margin-left: 10px">{{  scope.row.consignor_id}}</span>
              </template>
            </el-table-column>
            <el-table-column
              label="开户银行"
              width="140">
              <template slot-scope="scope">
                <span style="margin-left: 10px">{{  scope.row.opening_bank}}</span>
              </template>
            </el-table-column>
            <el-table-column
              label="银行账号"
              width="140">
              <template slot-scope="scope">
                <span style="margin-left: 10px">{{  scope.row.bank_account}}</span>
              </template>
            </el-table-column>
            <el-table-column
              label="企业地址"
              width="180">
              <template slot-scope="scope">
                <span style="margin-left: 10px">{{  scope.row.business_address}}</span>
              </template>
               </el-table-column>
              <el-table-column
              label="企业电话"
              width="100">
              <template slot-scope="scope">
                <span style="margin-left: 10px">{{  scope.row.enterprise_phone}}</span>
              </template>
            </el-table-column>
            <el-table-column
              label="操作"
              width="180">
               <template slot-scope="scope">
        <el-button
          size="mini"
          @click="changeById(scope.row.id,scope.row.type,scope.row.receiving_name,scope.row.consignor_id,scope.row.opening_bank,scope.row.bank_account,scope.row.business_address,scope.row.enterprise_phone)">编辑</el-button>
        <el-button
          size="mini"
          type="danger"
          @click="centerDialogVisible = true">删除</el-button>
          <el-dialog
                    title="提示"
                    :visible.sync="centerDialogVisible"
                    width="30%"
                    center>
                    <span>确认删除此发票抬头？</span>
                    <span slot="footer" class="dialog-footer">
                      <el-button @click="centerDialogVisible = false">取 消</el-button>
                      <el-button type="danger"  @click="doOptions(API.API_URL_CONSIGNOR_REMOVE,scope.row.id,'删除成功');centerDialogVisible = false">删 除</el-button>
                    </span>
                  </el-dialog>
      </template>
            </el-table-column>  
         </el-table>
          <div class="panel-body">
            <fieldset class="margin-top-10">
              <div class="row">
                <div class="col-md-6 col-sm-6">
                  <span class="type">请选择类型：</span>
                  <el-radio v-model="type" label="1" @change="change(type)" >增值税专用发票</el-radio>
                  <el-radio v-model="type" label="2" @change="change(type)">普通发票</el-radio>
                </div>
              </div>
              <div class="row">
                <div class="col-md-6 col-sm-6">
                  <input type="hidden" name="id">
                  <label for="billing_firstname">
                    单位名称&nbsp;
                    <span class="required">*(必填)</span>
                  </label>
                  <el-input id="billing_firstname" v-model="name" type="text" maxlength="50"/>
                  <div class="erro">{{tips.nameMsg}}</div>
                </div>
                <div class="col-md-6 col-sm-6">
                  <label for="billing_id">
                    纳税人识别号&nbsp;
                    <span class="required">*(必填)</span>
                  </label>
                  <el-input id="billing_id" v-model="id" type="number" maxlength="11"/>
                  <div class="erro">{{tips.idMsg}}</div>
                </div>
              </div>
              <div class="row">
                <div class="col-md-6 col-sm-6">
                  <input type="hidden" name="id">
                  <label for="billing_bank">
                    开户银行&nbsp;
                    <span class="required" v-show="isShow">*(必填)</span>
                  </label>
                  <el-input id="billing_bank" v-model="bank" type="text" maxlength="50"/>
                  <div class="erro">{{tips.bankMsg}}</div>
                </div>
                <div class="col-md-6 col-sm-6">
                  <label for="billing_account">
                    银行账号&nbsp;
                    <span class="required" v-show="isShow">*(必填)</span>
                  </label>
                  <el-input id="billing_account" v-model="account" type="number" maxlength="20"/>
                  <div class="erro">{{tips.accountMsg}}</div>
                </div>
              </div>
              <div class="row">
                <div class="col-md-6 col-sm-6">
                  <label for="billing_address1">
                    企业地址&nbsp;
                    <span class="required" v-show="isShow">*(必填)</span>
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
                <div class="col-md-6 col-sm-6">
                  <label for="billing_lastname">
                    企业电话&nbsp;
                    <span class="required" v-show="isShow">*(必填)</span>
                  </label>
                  <el-input id="billing_lastname" v-model="mobile" type="text" maxlength="12"/>
                  <div class="erro">{{tips.mobileMsg}}</div>
                </div>
              </div>
              <!-- <div class="row">
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
              </div> -->
              <el-button
                @click="addContact"
                type="primary"
                class="pull-right margin-top-20"
                icon="el-icon-edit"
              >保存信息</el-button>
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
      centerDialogVisible: false,
      isShow: true,
      type: "1",
      type1: "增值税专用发票",
      type2: "普通发票",
      id: null,
      accountList: null,
      name: null,
      bank: null,
      mobile: null,
      account: null,
      address: null,
      editFlag: false,
      editId: null,
      tips: {
        nameMsg: "",
        mobileMsg: "",
        idMsg: "",
        addressMsg: "",
        accountMsg: "",
        bankMsg: ""
      }
    };
  },
  created() {
    this.getAccountList();
  },
  methods: {
    change(type) {
      var vm = this;
      console.log(type);
      vm.tips.mobileMsg = "";
      vm.tips.addressMsg = "";
      vm.tips.idMsg = "";
      vm.tips.bankMsg = "";
      vm.tips.accountMsg = "";
      vm.tips.nameMsg = "";
      type == 1 ? (this.isShow = true) : (this.isShow = false);
      console.log(this.isShow);
    },
    getAccountList() {
      var vm = this;
      var user = vm.Storage.Session.get("User");
      if (user) {
        var userId = user.id;
        vm.$post(vm.API.API_URL_CONSIGNOR, { userId: userId }).then(res => {
          if (res.errorCode == 0 && res.data != null) {
            vm.accountList = res.data;
            console.log(res);
            console.log(vm.accountList.data);
          }
        });
      }
    },
    changeById(editId, type, name, id, bank, account, address, mobile) {
      this.editId = editId;
      this.type = type + "";
      this.name = name;
      this.id = id;
      this.bank = bank;
      this.account = account;
      this.address = address;
      this.mobile = mobile;
      this.editFlag = true;
    },
    addContact() {
      var url = this.API.API_URL_CONSIGNOR_ADD;
      var msg1 = "添加成功!";
      var msg2 = "发票抬头至多添加5条!";
      if (this.editFlag) {
        url = this.API.API_URL_CONSIGNOR_UPDATE;
        msg1 = "修改成功!";
        msg2 = "修改失败!";
      }
      var mobile = /^400-[016789]\d{2}-\d{4}$/;
      var vm = this;
      if (
        !vm.name &&
        !vm.id &&
        !vm.bank &&
        !vm.account &&
        !vm.address &&
        !vm.mobile
      ) {
        vm.tips.nameMsg = "请输入单位名称";
        vm.tips.idMsg = "请输入纳税人识别码";
        if (this.isShow) {
          vm.tips.bankMsg = "请输入开户银行";
          vm.tips.accountMsg = "请输入银行账号信息";
          vm.tips.addressMsg = "请输入企业地址";
          vm.tips.mobileMsg = "请输入企业电话";
        }
      } else if (!vm.name) {
        vm.tips.mobileMsg = "";
        vm.tips.addressMsg = "";
        vm.tips.idMsg = "";
        vm.tips.bankMsg = "";
        vm.tips.accountMsg = "";
        vm.tips.nameMsg = "请输入单位名称";
      } else if (!vm.id) {
        vm.tips.nameMsg = "";
        vm.tips.idMsg = "请输入纳税人识别码";
        vm.tips.bankMsg = "";
        vm.tips.accountMsg = "";
        vm.tips.addressMsg = "";
        vm.tips.mobileMsg = "";
      } else if (!vm.bank && this.isShow) {
        vm.tips.nameMsg = "";
        vm.tips.idMsg = "";
        vm.tips.bankMsg = "请输入开户银行";
        vm.tips.accountMsg = "";
        vm.tips.addressMsg = "";
        vm.tips.mobileMsg = "";
      } else if (!vm.account && this.isShow) {
        vm.tips.nameMsg = "";
        vm.tips.idMsg = "";
        vm.tips.bankMsg = "";
        vm.tips.accountMsg = "请输入银行账号";
        vm.tips.addressMsg = "";
        vm.tips.mobileMsg = "";
      } else if (!vm.address && this.isShow) {
        vm.tips.nameMsg = "";
        vm.tips.idMsg = "";
        vm.tips.bankMsg = "";
        vm.tips.accountMsg = "";
        vm.tips.addressMsg = "请输入企业地址";
        vm.tips.mobileMsg = "";
      } else if (!vm.mobile && this.isShow) {
        vm.tips.nameMsg = "";
        vm.tips.idMsg = "";
        vm.tips.bankMsg = "";
        vm.tips.accountMsg = "";
        vm.tips.addressMsg = "";
        vm.tips.mobileMsg = "请输入企业电话";
      } else if (
        (!mobile.test(vm.mobile) || vm.mobile.length != 12) &&
        vm.mobile
      ) {
        vm.tips.nameMsg = "";
        vm.tips.idMsg = "";
        vm.tips.bankMsg = "";
        vm.tips.accountMsg = "";
        vm.tips.addressMsg = "";
        vm.tips.mobileMsg = "请输入正确的企业电话(400-(0/1/6/7/8/9)XX-XXXX)";
      } else {
        vm.tips.nameMsg = "";
        vm.tips.idMsg = "";
        vm.tips.bankMsg = "";
        vm.tips.accountMsg = "";
        vm.tips.addressMsg = "";
        vm.tips.mobileMsg = "";
        var user = vm.Storage.Session.get("User");
        if (user) {
          var userId = user.id;
          vm.$post(url, {
            id: vm.editId,
            name: vm.name,
            userId: userId,
            cs_id: vm.id,
            bank: vm.bank,
            account: vm.account,
            address: vm.address,
            mobile: vm.mobile,
            type: vm.type
          }).then(res => {
            console.log(res);
            if (res.errorCode == 0) {
              if (res.data) {
                vm.getAccountList();
                vm.name = "";
                vm.mobile = "";
                vm.address = "";
                vm.account = "";
                vm.bank = "";
                vm.id = "";
                vm.Toastr.success(msg1);
                if (vm.editFlag) {
                  vm.editId = null;
                  vm.editFlag = false;
                }
              }
            } else {
              vm.Toastr.error(msg2);
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
.type {
  font-size: 15px;
  color: #000;
  font-weight: bold;
}
</style>
