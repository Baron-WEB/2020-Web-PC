<template>
  <div id="app-content">
    <section class="page-header page-header-xs">
      <div class="container">
        <h1>我的购物车</h1>
        <el-breadcrumb separator-class="el-icon-arrow-right">
          <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
          <el-breadcrumb-item>购物车</el-breadcrumb-item>
        </el-breadcrumb>
      </div>
    </section>
    <!-- /PAGE HEADER -->
    <!-- 购物车 -->
    <section>
      <!-- <div class="container" v-if="cartList"> -->
      <div class="container" v-if="cartList">
        <!-- 如果购物车为空 -->
        <div class="panel panel-default" v-if="cartList.productList.length==0">
        <!-- <div class="panel panel-default" v-if="true"> -->
          <div class="panel-body">
            <strong style="font-size: 25px">购物车里无商品!</strong>
            <br>随便看看，优惠多多，赶紧抢购吧！
          </div>
        </div>
        <!-- 如果购物车不为空 -->
        <!-- CART -->
        <div class="row" v-else>
          <!-- LEFT -->
          <div class="col-lg-9 col-sm-8">
            <!-- CART -->
            <!-- 原版购物车左侧 -->
            <form class="cartContent clearfix" method="post" action="#" v-if="false">
              <!-- cart content -->
              <div id="cartContent">
                <!-- cart header -->
                <div class="item head clearfix">
                  <!-- <span class="cart_img"></span>
                  <span class="product_name size-13 bold">产品名称</span>
                  <span class="remove_item size-13 bold"></span>
                  <span class="total_price size-13 bold">合计价格</span>
                  <span class="qty size-13 bold">数量</span> -->
                  <span class="product_name size-13 bold">产品名称</span>
                  <span class="remove_item size-13 bold"></span>
                  <span class="remove_item size-13 bold"></span>
                  <span class="total_price size-13 bold">合计价格</span>
                  <span class="qty size-13 bold">数量</span>
                </div>
                <!-- /cart header -->
                <template>
                  <div class="item" v-for="product in cartList.productList">
                    <!-- 商品图片 -->
                    <div class="cart_img pull-left width-100 padding-10 text-left">
                      <img v-bind:src="API.BASE_SERVER_URL+product.defaultImg" alt width="40">
                    </div>
                    <!-- 商品图片名 -->
                    <a @click="goto('/product','Product',product.id)" class="product_name">
                      <span>{{product.name}}</span>
                    </a>
                    <!-- 清除按钮 -->
                    <el-button
                      @click="removeCartItem(product.id)"
                      style="margin: 10px;float: right"
                      type="danger"
                      icon="el-icon-delete"
                      circle
                    ></el-button>
                    <!-- 合计价格 -->
                    <div name="total_price" class="total_price">
                      ¥
                      <span>{{(product.shopPrice*product.quantity).toFixed(2)}}</span>
                    </div>
                     <!-- 数量 -->
                    <div class="qty">
                      <el-input-number
                        size="mini"
                        v-model="product.quantity"
                        @change="updateQuantity"
                        :min="1"
                        :max="999"
                      ></el-input-number>
                      &times;
                      ¥ {{(product.shopPrice).toFixed(2)}}
                    </div>
                    <!-- 清除浮动 -->
                    <div class="clearfix"></div>
                  </div>
                </template>

                <el-button
                  @click="updateCartOrPay"
                  style="margin-left: 10px"
                  type="success"
                  plain
                  class="pull-right margin-top-20"
                  icon="el-icon-check"
                >更新购物车</el-button>
                <el-button
                  @click="removeCartAll"
                  type="danger"
                  plain
                  class="pull-right margin-top-20"
                  icon="el-icon-delete"
                >清空购物车</el-button>

                <!-- /update cart -->
                <div class="clearfix"></div>
              </div>
              <!-- /cart content -->
            </form>

            <!-- 优化版购物车左侧列表 -->
            <template>
              <el-table
                :data="cartList.productList"
                style="width: 100%">
                <el-table-column
                  label="商品名称"
                  width="430px">
                  <template slot-scope="scope">
                      <img v-bind:src="API.BASE_SERVER_URL+scope.row.defaultImg" alt width="60">
                    <a @click="goto('/product','Product',scope.row.id)" class="product_name">
                      <span>{{scope.row.name}}</span>
                    </a>
                  </template>
                </el-table-column>
                <el-table-column
                  label="数量"
                  width="230px">
                  <template slot-scope="scope">
                    <el-input-number
                        size="mini"
                        v-model="scope.row.quantity"
                        @change="updateQuantity"
                        :min="1"
                        :max="999"
                      ></el-input-number>
                      &times;
                      ¥ {{(scope.row.shopPrice).toFixed(2)}}
                  </template>
                </el-table-column>
                <el-table-column label="合计价格" width="80px">
                  <template slot-scope="scope">
                     ¥<span>{{(scope.row.shopPrice*scope.row.quantity).toFixed(2)}}</span>
                  </template>
                </el-table-column>
                <el-table-column label="操作" width="100px">
                  <template slot-scope="scope">
                    <el-button
                      size="mini"
                      type="danger"
                      @click="removeCartItem(scope.row.id)">删除</el-button>
                       <!-- <el-button type="danger" icon="el-icon-delete" circle></el-button>s -->
                  </template>
                </el-table-column>
              </el-table>
            </template>
            <el-button
                  @click="updateCartOrPay"
                  style="margin-left: 10px"
                  type="success"
                  plain
                  class="pull-right margin-top-20"
                  icon="el-icon-check"
                >更新购物车</el-button>
            <el-button
                  @click="removeCartAll"
                  type="danger"
                  plain
                  class="pull-right margin-top-20"
                  icon="el-icon-delete"
            >清空购物车</el-button>
            <!-- /CART -->
          </div>
          <!-- RIGHT -->
          <div class="col-lg-3 col-sm-4">
            <div>
              <div class="hideDiv">
                <span class="clearfix">
                  <span id="sumCart" class="pull-right">¥ {{(cartList.price).toFixed(2)}}</span>
                  <strong class="pull-left">商品总价:</strong>
                </span>
                <span class="clearfix">
                  <span class="pull-right">包邮</span>
                  <span class="pull-left">物流费用:</span>
                </span>
                <hr>
                <span class="clearfix">
                  <span class="pull-right size-20">¥{{(cartList.price).toFixed(2)}}</span>
                  <strong class="pull-left">合计金额:</strong>
                </span>
                <hr>
                <el-button
                  @click="cartOrOrder"
                  style="margin-left: 10px"
                  type="success"
                  plain
                  class="pull-right margin-top-20"
                  icon="el-icon-check"
                >立即支付</el-button>
              </div>
            </div>
          </div>
        </div>
        <!-- /CART -->
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: "Cart",
  data() {
    return {
      cartList: {
        productList: []
      }
    };
  },
  created() {
    this.getCartList();
  },
  methods: {
    getCartList() {
      var vm = this;
      var user = vm.Storage.Session.get("User");
      if (user) {
        var userId = user.id;
        if (user) {
          var userId = user.id;
          vm.$post(vm.API.API_URL_CART, { userId: userId }).then(res => {
            if (res.errorCode == 0 && res.data) {
              vm.cartList = res.data;
              console.log(vm.cartList.productList);
            } else {
              vm.cartList.orderItemList = [];
            }
          });
        }
      }
    },
    removeCartItem(productId) {
      var vm = this;
      var user = this.Storage.Session.get("User");
      if (user) {
        vm.$post(this.API.API_URL_CART_ITEMS_REMOVE, {
          userId: user.id,
          productId: productId
        }).then(res => {
          if (res.errorCode == 0 && res.data) {
            vm.getCartList();
            vm.Toastr.success("删除成功");
          } else {
            vm.Toastr.error("删除失败");
          }
        });
      } else {
        this.Toastr.warning("请登录后再操作!");
      }
    },
    removeCartAll() {
      var vm = this;
      var user = this.Storage.Session.get("User");
      if (user) {
        vm.$post(this.API.API_URL_CART_ITEMS_REMOVEALL, {
          userId: user.id
        }).then(res => {
          if (res.errorCode == 0 && res.data) {
            vm.getCartList();
            vm.$store.commit("setCartCount", vm.getCartCount());
          } else {
            vm.Toastr.error("清空失败");
          }
        });
      } else {
        this.Toastr.warning("请登录后再操作!");
      }
    },
    updateQuantity(quantity) {
      if (this.cartList) {
        this.cartList.price = 0;
        var cartItemList = this.cartList.productList;
        for (var cartItem in cartItemList) {
          if (cartItemList[cartItem].quantity == "") {
            cartItemList[cartItem].quantity = 1;
          }
          this.cartList.price +=
            cartItemList[cartItem].shopPrice * cartItemList[cartItem].quantity;
        }
      }
    },
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
    goto(path, name, val) {
      this.$router.push({
        path: path,
        name: name
      });
      if (val) {
        this.Storage.Session.set("data", { id: val });
      }
    },
    updateCartOrPay(flag) {
      var vm = this;
      var user = vm.Storage.Session.get("User");
      if (user) {
        var userId = user.id;
        var cartItemList = [];
        var cartItem = {};
        var cartList = this.cartList.productList;
        for (var cart in cartList) {
          var cartItem = {};
          cartItem["productId"] = cartList[cart].id;
          cartItem["quantity"] = cartList[cart].quantity;
          cartItem["skuId"] = cartList.skuId;
          cartItem["optionValueKeyGroup"] = cartItem.optionValueKeyGroup;
          cartItemList.push(cartItem);
        }
      }
      vm.$post(vm.API.API_URL_CART_ITEMS_UPDATE, {
        userId: userId,
        cartItemList: cartItemList
      }).then(res => {
        if (res.errorCode == 0 && res.data == 1) {
          vm.getCartList();
          vm.Toastr.success("更新成功！");
        } else {
          vm.Toastr.error("更新失败！");
        }
      });
    },
    cartOrOrder() {
      var vm = this;
      var user = vm.Storage.Session.get("User");
      if (user) {
        var userId = user.id;
        var orderItemList = [];
        var orderList = this.cartList.productList;
        for (var order in orderList) {
          var orderItem = {};
          orderItem["productId"] = orderList[order].id;
          orderItem["quantity"] = orderList[order].quantity;
          orderItemList.push(orderItem);
        }
      }
      vm.$post(vm.API.API_URL_CART_COMMIT, {
        userId: userId,
        orderItemList: orderItemList
      }).then(res => {
        if (res.errorCode == 0 && res.errorMsg == "成功") {
          vm.removeCartAll();
          vm.$router.push({ path: "/account/payment", name: "Payment" });
        } else {
          vm.Toastr.warning("生成订单失败!");
        }
      });
    }
  }
};
</script>

<style scoped>

</style>
