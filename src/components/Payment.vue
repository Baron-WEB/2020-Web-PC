<template>
  <div id="app-content">
    <section class="page-header page-header-xs">
      <div class="container">
        <h1>支付</h1>
        <ol class="breadcrumb">
          <li>
            <a href="/">首页</a>
          </li>
          <li>支付</li>
        </ol>
      </div>
    </section>
    <!-- /PAGE HEADER -->
    <!-- 购物车 -->
    <section>
      <div class="container" v-if="payment">
        <div class="row">
          <!-- LEFT -->
          <div class="col-lg-9 col-sm-8">
            <!-- CART -->
            <!-- 优化版购物车左侧 -->
            <template v-for="p in payment.orderItemList">
              <el-table
                :data="p.productList"
                style="width: 100%">
                <el-table-column
                  label="商品名称"
                  width="430px">
                  <template slot-scope="scope">
                      <img v-bind:src="API.BASE_SERVER_URL+scope.row.defaultImg" alt width="60">
                    <a @click="goto('/product','Product',scope.row.id)" class="product_name">
                      <span>{{scope.row.name}}</span>
                      <small>{{scope.row.jsonData}}</small>
                    </a>
                  </template>
                </el-table-column>
                <el-table-column
                  label="数量"
                  width="230px">
                  <template slot-scope="scope">
                    <p
                        class="zq"
                        zid="${z.id}"
                        zpid="${z.productList[0].id}"
                        sku="${z.skuId}"
                        group="${z.optionValueKeyGroup}"
                      >{{p.quantity}}</p>
                      &times;{{p.price.toFixed(2)}}
                  </template>
                </el-table-column>
                <el-table-column label="合计价格" width="80px">
                  <template slot-scope="scope">
                     ¥<span>{{(p.price*p.quantity).toFixed(2)}}</span>
                  </template>
                </el-table-column>
              </el-table>
            </template>
                <!-- <template v-for="p in payment.orderItemList">
                  <div class="item" v-for="product in p.productList">
                    <div class="cart_img pull-left width-100 padding-10 text-left">
                      <img v-bind:src="API.BASE_SERVER_URL+product.defaultImg" alt width="40">
                    </div>
                    <a href="shop-single-left.html" class="product_name">
                      <span>{{product.name}}</span>
                      <small>{{product.jsonData}}</small>
                    </a>
                    <div name="total_price" class="total_price">
                      ￥
                      <span>{{(p.price*p.quantity).toFixed(2)}}</span>
                    </div>
                    <div class="qty">
                      <p
                        class="zq"
                        zid="${z.id}"
                        zpid="${z.productList[0].id}"
                        sku="${z.skuId}"
                        group="${z.optionValueKeyGroup}"
                      >{{p.quantity}}</p>
                      &times;{{p.price.toFixed(2)}}
                    </div>
                    <div class="clearfix"></div>
                  </div>
                </template> -->
                <!-- 常用联系人 -->
                <div class="padding-20">
                  <strong style="font-size: 20px">选择常用联系人</strong>
                  <hr>
                  <div class="padding-bottom-10" v-for="contect in payment.contectList">
                    <input
                      v-if="contect.contactFlag==1"
                      class="product-review-vote"
                      type="radio"
                      checked="checked"
                      name="contact"
                      @click="selectContact(contect.id)"
                    >
                    <input
                      v-else
                      class="product-review-vote"
                      type="radio"
                      name="contact"
                      @click="selectContact(contect.id)"
                    >
                    姓名：{{contect.name}} &nbsp;&nbsp; 联系电话：{{contect.mobile}} &nbsp;&nbsp; 地址：{{contect.address}}
                  </div>
                  <!-- 联系人 -->
                </div>
                <!-- 发票 -->
                <div class="padding-20">
                  <strong style="font-size: 20px">选择是否需要发票</strong>
                  <hr>
                  <div class="padding-bottom-10">
                    <input
                      id="invoiceYes"
                      ref="invoiceYes"
                      class="product-review-vote"
                      style="float:left;"
                      type="radio"
                      name="kkk"
                      value="1"
                      v-model="picked"
                      @click="invoicePick"
                    >
                    <label style="float:left;margin-right: 30px;padding-left: 5px;" for="invoiceYes">是</label>
                    <input
                      id="invoiceNo"
                      ref="invoiceNo"
                      class="product-review-vote"
                      style="float:left;"
                      type="radio"
                      name="kkk"
                      value="2"
                      checked="checked"
                      v-model="picked"
                    >
                    <label style="float:left;padding-left: 5px;" for="invoiceNo">否</label>
                    <div class="invoiceWrap" v-if="picked==1&&invoicelist.length>0">
                      <ol class="invcItemList">
                        <li @click="pickItem(item.id)" v-for="(item,index) in invoicelist">
                          <div>
                            <input
                              class="product-review-vote"
                              type="radio"
                              name="itemRadio"
                              :value="index"
                            >
                          </div>
                          <div>单位名称：<span>{{item.receivingName}}</span></div>
                          <div v-if="item.type==1">纳税人识别号：<span>{{item.consignorId}}</span></div>
                        </li>
                      </ol>
                    </div>
                  </div>
                </div>
                <!-- 支付方式 -->
                <div class="padding-20">
                  <strong style="font-size: 20px">请选择支付方式</strong>
                  <hr>
                  <div>
                    <a @click="payTreasure(0)">
                      <img src="../../static/images/pay/pc_wxqrpay.png">
                    </a>
                    <a @click="qrcode">
                      <img src="../../static/images/pay/alipaypcnew.png">
                    </a>
                    <a @click="payTreasure(2)">电子钱包</a>
                  </div>
                </div>
              <!-- 支付宝二维码 -->
              <div class="qrcode">
                <qr
                  v-show="qrcodeState"
                  style="text-align: center"
                  :logoSrc="require('../../static/images/pay/alipaypcnew.png')"
                  :text="qrdata"
                  :size="200"
                  :margin="0"
                ></qr>
              </div>

            <!-- 原版购物车左侧 -->
            <form class="cartContent clearfix" method="post" action="#" v-if="false">
              <div id="cartContent">
                <!-- cart header -->
                <div class="item head clearfix">
                  <span class="cart_img"></span>
                  <span class="product_name size-13 bold">产品名称</span>
                  <span class="remove_item size-13 bold"></span>
                  <span class="total_price size-13 bold">合计价格</span>
                  <span class="qty size-13 bold">数量</span>
                </div>
                <!-- /cart header -->
                <template v-for="p in payment.orderItemList">
                  <div class="item" v-for="product in p.productList">
                    <div class="cart_img pull-left width-100 padding-10 text-left">
                      <img v-bind:src="API.BASE_SERVER_URL+product.defaultImg" alt width="40">
                    </div>
                    <a href="shop-single-left.html" class="product_name">
                      <span>{{product.name}}</span>
                      <small>{{product.jsonData}}</small>
                    </a>
                    <div name="total_price" class="total_price">
                      ￥
                      <span>{{(p.price*p.quantity).toFixed(2)}}</span>
                    </div>
                    <div class="qty">
                      <p
                        class="zq"
                        zid="${z.id}"
                        zpid="${z.productList[0].id}"
                        sku="${z.skuId}"
                        group="${z.optionValueKeyGroup}"
                      >{{p.quantity}}</p>
                      &times;{{p.price.toFixed(2)}}
                    </div>
                    <div class="clearfix"></div>
                  </div>
                </template>
                <!-- 常用联系人 -->
                <div class="padding-20">
                  <strong style="font-size: 20px">选择常用联系人</strong>
                  <hr>
                  <div class="padding-bottom-10" v-for="contect in payment.contectList">
                    <input
                      v-if="contect.contactFlag==1"
                      class="product-review-vote"
                      type="radio"
                      checked="checked"
                      name="contact"
                      @click="selectContact(contect.id)"
                    >
                    <input
                      v-else
                      class="product-review-vote"
                      type="radio"
                      name="contact"
                      @click="selectContact(contect.id)"
                    >
                    姓名：{{contect.name}} &nbsp;&nbsp; 联系电话：{{contect.mobile}} &nbsp;&nbsp; 地址：{{contect.address}}
                  </div>
                  <!-- 联系人 -->
                </div>
                <!-- 发票 -->
                <div class="padding-20">
                  <strong style="font-size: 20px">选择是否需要发票</strong>
                  <hr>
                  <div class="padding-bottom-10">
                    <input
                      id="invoiceYes"
                      ref="invoiceYes"
                      class="product-review-vote"
                      style="float:left;"
                      type="radio"
                      name="kkk"
                      value="1"
                      v-model="picked"
                      @click="invoicePick"
                    >
                    <label style="float:left;margin-right: 30px;padding-left: 5px;" for="invoiceYes">是</label>
                    <input
                      id="invoiceNo"
                      ref="invoiceNo"
                      class="product-review-vote"
                      style="float:left;"
                      type="radio"
                      name="kkk"
                      value="2"
                      checked="checked"
                      v-model="picked"
                    >
                    <label style="float:left;padding-left: 5px;" for="invoiceNo">否</label>
                    <div class="invoiceWrap" v-if="picked==1&&invoicelist.length>0">
                      <ol class="invcItemList">
                        <li @click="pickItem(item.id)" v-for="(item,index) in invoicelist">
                          <div>
                            <input
                              class="product-review-vote"
                              type="radio"
                              name="itemRadio"
                              :value="index"
                            >
                          </div>
                          <div>单位名称：<span>{{item.receivingName}}</span></div>
                          <div v-if="item.type==1">纳税人识别号：<span>{{item.consignorId}}</span></div>
                        </li>
                      </ol>
                    </div>
                  </div>
                </div>
                <!-- 支付方式 -->
                <div class="padding-20">
                  <strong style="font-size: 20px">请选择支付方式</strong>
                  <hr>
                  <div>
                    <a @click="payTreasure(0)">
                      <img src="../../static/images/pay/pc_wxqrpay.png">
                    </a>
                    <a @click="qrcode">
                      <img src="../../static/images/pay/alipaypcnew.png">
                    </a>
                    <a @click="payTreasure(2)">电子钱包</a>
                  </div>
                </div>
                <div class="clearfix"></div>
              </div>
              <!-- 支付宝二维码 -->
              <div class="qrcode">
                <qr
                  v-show="qrcodeState"
                  style="text-align: center"
                  :logoSrc="require('../../static/images/pay/alipaypcnew.png')"
                  :text="qrdata"
                  :size="200"
                  :margin="0"
                ></qr>
              </div>
            </form>
            <!-- /cart content -->
            <!-- /CART -->
          </div>
          <!-- RIGHT -->
          <div class="col-lg-3 col-sm-4">
            <div class="toggle active">
              <div class="toggle-content" style="display: block;">
                <div>
                  <span class="clearfix">
                    <span class="pull-right">￥{{payment.price.toFixed(2)}}</span>
                    <strong class="pull-left">商品总价:</strong>
                  </span>
                  <span class="clearfix">
                    <span class="pull-right">包邮</span>
                    <span class="pull-left">物流费用:</span>
                  </span>
                  <hr>
                  <span class="clearfix">
                    <span class="pull-right size-20">￥{{payment.price.toFixed(2)}}</span>
                    <strong class="pull-left">合计金额:</strong>
                  </span>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import qr from "vue-qr";

export default {
  name: "Payment",
  data() {
    return {
      payment: null,
      selectContactId: null,
      qrcodeState: false,
      picked: "",
      invoicelist: null,
      itemNum: null,
      check: null
    };
  },
  created() {
    this.getCartList();
    this.getInvoice();
  },
  components: { qr },
  destroyed() {
    clearInterval(this.check);
  },
  computed: {
    qrdata() {
      return JSON.stringify({
        orderNum: this.payment.orderNum,
        userId: this.Storage.Session.get("User").id,
        id: this.selectContactId,
        paymentType: 1
      });
    }
  },
  methods: {
    qrcode() {
      var vm = this,
        id = this.Storage.Session.get("User").id,
        num = this.payment.orderNum
      this.qrcodeState = true;
      this.check = setInterval(function() {
        vm.$post(vm.API.API_CHECK_ORDER, {
          userId: id,
          orderNum: num
        }).then(res => {
          if (res.data == 1 ) {
            vm.Toastr.success("支付成功，2秒钟后跳转到订单历史");
            vm.$store.commit("setCartCount", vm.getCartCount());
            vm.$router.push({
              path: "/account/order/list",
              name: "OrderList"
            });
          } else {
            // vm.Toastr.error("支付失败");
          }
        });
      }, 2000);
    },
    getCartList() {
      var vm = this;
      var user = vm.Storage.Session.get("User");
      if (user) {
        var userId = user.id;
        vm.$post(vm.API.API_URL_CART_ORDER, {
          userId: userId,
          type: 1
        }).then(res => {
          if (res.errorCode == 0 && res.data) {
            vm.payment = res.data;
            for (var item in res.data.contectList) {
              if (res.data.contectList[item].contactFlag == 1) {
                vm.selectContactId = res.data.contectList[item].id;
                console.log(res.data.contectList[item].id);
              }
            }
          }
        });
      }
    },
    selectContact(contactId) {
      this.selectContactId = contactId;
    },
    payTreasure(type) {
      this.qrcode = false;
      var vm = this;
      var user = vm.Storage.Session.get("User");
      var num = vm.payment.orderNum;
      console.log(num);
      var amount = 0;
      var timer = null;
      clearInterval(timer);
      function loadAmount() {
        var p = new Promise(function(resolve, reject) {
          vm.$post(vm.API.API_URL_CUSTOMER_Account, {
            userId: user.id
          }).then(res => {
            if (res.errorCode == 0 && res.data) {
              amount = res.data.amount;
              resolve(amount);
            }
          });
        });
        return p;
      }
      loadAmount().then(function() {
        if (user) {
          if (type == 2 && amount < vm.payment.price) {
            vm.Toastr.error("电子钱包 余额不足！");
            vm.$router.push({
              path: "/account/ewallet",
              name: "EWallet"
            });
            return;
          }
          if (type == 0 && amount < vm.payment.price) {
            vm.Toastr.error("微信 余额不足！");
            vm.$router.push({
              path: "/account/ewallet",
              name: "EWallet"
            });
            return;
          }
          if (!vm.selectContactId) {
            vm.Toastr.warning("请选择联系人！");
            return;
          }
          vm.$post(vm.API.API_URL_ORDER_PAYMENT, {
            orderNum: num,
            userId: user.id,
            id: vm.selectContactId,
            paymentType: type
          }).then(res => {
            //errorCode为0则为成功
            if (res.errorCode == 0) {
              vm.Toastr.success("支付成功，2秒钟后跳转到订单历史");
              vm.$store.commit("setCartCount", vm.getCartCount());

              if ((vm.picked = !null && vm.picked == 1)) {
                var num = vm.payment.orderNum;
                vm.$post(vm.API.API_URL_AddInvoiceList, {
                  orderNum: num,
                  receivingId: vm.itemNum
                }).then(res => {
                  if (res.errorCode == 0) {
                    console.log("发送抬头信息成功");
                  }
                });
              }

              timer = setTimeout(function() {
                if (type == 2) {
                  vm.$router.push({
                    path: "/account/paymentResult",
                    name: "PaymentResult"
                  });
                  vm.Storage.Session.set("TotalPrice", vm.payment.price);
                } else {
                  vm.$router.push({
                    path: "/account/order/list",
                    name: "OrderList"
                  });
                }
              }, 2000);
            } else {
              vm.Toastr.error(res.errorMsg);
            }
          });
        }
      });
    },
    getUserAccount() {
      var vm = this;
      var user = vm.Storage.Session.get("User");
      var amount = 0;
      if (user) {
        vm.$post(vm.API.API_URL_CUSTOMER_Account, {
          userId: user.id
        }).then(res => {
          if (res.errorCode == 0 && res.data) {
            amount = res.data.amount;
          }
          return amount;
        });
      }
    },
    //获取购物车数量
    getCartCount() {
      var vm = this;
      var user = this.Storage.Session.get("User");
      var cartCount = 0;
      if (user) {
        var userId = user.id;
        vm.$post(vm.API.API_URL_CART_COUNT, {
          userId: userId
        }).then(res => {
          if (res.errorCode == 0 && res.data != null) {
            vm.cartCount = res.data;
            cartCount = res.data;
          }
          return cartCount;
        });
      }
    },
    //获取发票信息
    getInvoice() {
      var vm = this;
      var userId = this.Storage.Session.get("User").id;
      this.$post(vm.API.API_URL_getInvoice, {
        userId: userId
      }).then(res => {
        vm.invoicelist = res.data;
        console.log(res.data);
      });
    },
    invoicePick() {
      var vm = this;
      if (this.invoicelist.length == 0) {
        vm.Toastr.error("请去抬头管理添加信息");
        vm.$refs.invoiceYes.checked = false;
        vm.picked = null;
      }
    },
    pickItem(num) {
      this.itemNum = num;
    }
  }
};
</script>


<style scoped>
.invoiceWrap {
  clear: both;
}
.invcItemList {
  width: 100%;
  display: block;
}
.invcItemList li:after {
  content: "";
  display: block;
  clear: both;
}
.invcItemList li > div {
  float: left;
  margin-right: 30px;
}
.invcItemList li > div:first-child {
  margin-right: 5px;
}
</style>
