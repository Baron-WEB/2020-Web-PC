<template>
  <div id="app-content">
    <template v-if="productDetail">
      <section class="page-header page-header-xs">
        <div class="container">
          <h1>{{productDetail.name}}</h1>
        </div>
      </section>
      <!-- /PAGE HEADER -->
      <!-- -->
      <section>
        <div class="container">
          <div class="row">
            <!-- RIGHT -->
            <div class="col-lg-9 col-md-9 col-sm-9">
              <div class="row">
                <!-- IMAGE -->
                <div class="col-lg-6 col-sm-6">
                  <div class="thumbnail relative margin-bottom-3">
                    <figure id="zoom-primary" class="zoom" data-mode="mouseover" style="width: 100%;height: 100%">
                      <div class="swiper-container swiper-container-horizontal"
                           data-space-between='10'>
                        <div class="swiper-wrapper">
                          <div>
                            <div>
                              <div class="swiper-slide">
                                <img v-bind:src="API.BASE_SERVER_URL+productDetail.defaultImg" style="width: 100%;height: 100%"/>
                              </div>
                            </div>
                          </div>
                        </div>
                        <!-- <div class="swiper-pagination"></div> -->
                      </div>
                    </figure>
                  </div>
                  <!-- Thumbnails (required height:100px) -->
                  <div data-for="zoom-primary"
                       class="zoom-more owl-carousel owl-padding-3 featured"
                       data-plugin-options='{"singleItem": false, "autoPlay": false, "navigation": true, "pagination": false}'></div>
                  <!-- /Thumbnails -->
                </div>
                <!-- /IMAGE -->
                <!-- ITEM DESC -->
                <div class="col-lg-6 col-sm-6">
                  <!-- buttons -->
                  <div id="qrcode"></div>
                  <div class="pull-right">
                    <!-- replace data-item-id width the real item ID - used by js/view/demo.shop.js -->

                    <el-button type="danger" @click="addWish(productDetail.id)" plain icon="el-icon-star-on" circle></el-button>
                  </div>
                  <!-- /buttons -->
                  <!-- price -->
                  <div class="shop-item-price">
									<span class="line-through nopadding-left"> 市场价:￥<font
                    id="price">{{productDetail.price}}</font>
									</span>￥<font id="shopPrice">{{productDetail.shopPrice}}</font>
                  </div>
                  <!-- /price -->
                  <hr/>
                  <div class="clearfix margin-bottom-30">
									<span v-if="productDetail.quantity>0" class="pull-right text-success">
										<i class="fa fa-check"></i>有货
									</span>
                    <span v-else class="pull-right text-danger">
										<i class="glyphicon glyphicon-remove"> </i>无货
									</span>
                    <strong>商品简介:</strong>
                  </div>
                  <!-- short description -->
                  <p>{{productDetail.generalExplain}}</p>
                  <!-- /short description -->

                  <!-- countdown -->
                  <!-- display option -->
                  <template v-if="optionList!=null && optionList.length>0">
                    <div class="text-center" id="">
                      <h5>请选择购买商品选项</h5>
                      <div v-for="option in optionList">
                        <div class='optionGroup' option>
                          {{option.name}}:
                          <span v-for="optionValues in option.optionValuesList" v-bind:optionId="option.id"
                                v-bind:optionValueId='optionValues.id' v-bind:optionValueName='optionValues.name'
                                class='option' @click="optionSelected(optionValues.id)"
                                v-bind:class="{optionSelected:optionSelectId==optionValues.id}">{{optionValues.name}}</span>
                        </div>
                      </div>
                    </div>
                  </template>
                  <!-- /display option -->
                  <!-- /countdown -->

                  <!-- FORM -->
                  <div class="clearfix form-inline nomargin"></div>
                  <!-- /FORM -->
                  <hr/>
                  <div class='row'>
                    <div class='col-lg-4 col-sm-4' style='padding-top: 10px; text-align: right'>购买数量:</div>
                    <el-input-number style="margin-left: 10px" v-model="params.quantity" :min="1" :max="1000"
                                     label="描述文字"></el-input-number>
                  </div>
                  <div style='text-align: center; margin-top: 20px'>
                    <p class='padding-top-8'>
                      <el-button type="warning" @click="submit(1)" plain>加入购物车</el-button>
                      <el-button type="danger" @click="submit(2)" plain>立即购买</el-button>

                    </p>
                  </div>
                </div>
                <!-- /ITEM DESC -->
              </div>
              <ul class="nav nav-tabs nav-top-border margin-top-80" role="tablist">
                <li role="presentation" v-bind:class="{active:tabSelected}">
                  <a @click="tabSelect" role="tab" data-toggle="tab" aria-expanded="true">
                    商品描述
                  </a>
                </li>
                <li role="presentation" v-bind:class="{active: !tabSelected}">
                  <a @click="tabSelect" role="tab" data-toggle="tab" aria-expanded="false">
                    商品评论 ({{reviewsList.length}})
                  </a>
                </li>
              </ul>
              <div class="tab-content padding-top-20">
                <!-- DESCRIPTION -->
                <div role="tabpanel" class="tab-pane fade" v-bind:class="{in:tabSelected,active:tabSelected}"
                     v-html="productDetail.explain">

                </div>
                <!-- REVIEWS -->
                <div role="tabpanel" class="tab-pane fade" v-bind:class="{in:!tabSelected,active:!tabSelected}">
                  <!-- REVIEW ITEM -->
                  <template v-for="reviews in reviewsList">
                    <div class="block margin-bottom-60">
                      <div class="media-body">
                        <h4 class="media-heading size-14">
                          {{reviews.user.nickname}} &ndash;
                          <span class="text-muted"> {{reviews.createTime}} </span>&ndash;
                          <span class="size-14 text-muted">
                          <el-rate v-model="reviews.stars" :colors="['#f56c6c','#f56c6c','#f56c6c']" disabled
                                   style="display: inline-block"></el-rate>

												</span>
                        </h4>
                        <p>{{reviews.content}}</p>
                      </div>
                    </div>
                  </template>
                  <!-- /REVIEW ITEM -->
                  <!-- REVIEW FORM -->
                  <h4 class="page-header margin-bottom-40">添加评论</h4>
                  <!-- Comment -->
                  <div class="margin-bottom-30">
                    <el-input v-model="reviews.content" :autosize="{ minRows: 2, maxRows: 4}"
                              placeholder="评论内容"></el-input>
                  </div>
                  <!-- Stars -->
                  <el-rate v-model="reviews.stars" :colors="['#409EFF','#e6a23c','#f56c6c']"></el-rate>
                  <el-button @click="addReviews" type="primary" class="pull-right margin-top-20"
                             icon="el-icon-edit">提交
                  </el-button>
                  <!-- Send Button -->
                  <!-- /REVIEW FORM -->
                </div>
              </div>
            </div>
            <!-- LEFT -->
            <div class="col-lg-3 col-md-3 col-sm-3">
              <!-- FEATURED -->
              <div class="margin-bottom-60">
                <h2 class="owl-featured">热销商品</h2>
                <div class="">
                  <div>
                    <!-- SLIDE -->
                    <ul class="list-unstyled nomargin nopadding text-left">
                      <template v-if="productHots&&productHots.length>0">
                        <li class="clearfix" v-for="productHot in productHots">
                          <!-- item -->
                          <div class="thumbnail featured clearfix pull-left">
                            <a @click="getDetail(productHot.id)">
                              <img v-bind:src="API.BASE_SERVER_URL+productHot.defaultImg" width="80" height="80"
                                   alt="featured item">
                            </a>
                          </div>
                          <a class="block size-12">{{productHot.name}}</a>
                          <div class="size-18 text-left padding-top-8">￥{{productHot.shopPrice}}
                          </div>
                          <span class="line-through nopadding-left">市场价:￥{{productHot.price}}</span>
                        </li>
                      </template>
                      <!-- /item -->
                    </ul>
                  </div>
                  <!-- /SLIDE -->
                </div>
              </div>
              <!-- /FEATURED -->
              <!-- HTML BLOCK -->
            </div>
          </div>
        </div>
      </section>
    </template>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        id: null,
        productDetail: null,
        productHots: [],
        optionList: [],
        skuList: null,
        reviewsList: [],
        tabSelected: true,
        optionSelectId: null,
        reviews: {
          content: '',
          stars: 0,
          productId: null
        },
        params: {
          userId: null,
          productId: null,
          quantity: 1,
          skuId: null,
          jsonData: null,
          optionValueKeyGroup: null
        }
      }
    },
    computed: {
      getCartProductId () {
        return this.$store.state.cartProductId
      }
    },
    created: function () {
      // console.log(this.$route.params.id);
      // var params = this.Storage.Session.get ('data')
      var params = this.$route.params
      if (params) {
        this.id = params.id
        this.productDetailList (params.id)
        this.productHotList (params.id, 1, 3)
        this.getSkuList (params.id)
        this.loadreviewsList (params.id)
        this.reviews.productId = params.id
        this.params.productId = params.id
      }
      this.getCode()
    },
    methods: {
      getCode() {
        setTimeout(()=> {
            //  console.log(this.jQuery('#qrcode'));
             let url = "http://127.0.0.1:9000/#/product/"+this.id
            //  let url = this.API.BASE_SERVER_URL_RAW+"/#/product/"+this.id
             console.log(url);
             this.jQuery('#qrcode').qrcode(url)
            //  console.log(this.jQuery('#qrcode'));
        },700)
      },
      //获取商品详细信息
      
      productDetailList (productId) {
        var vm = this;
        var user = this.Storage.Session.get ('User')
        vm.$post (vm.API.API_URL_CATALOG_PRODUCT_DETAILS,{
          productId : productId,
          // userId : user.id
        }).then(res =>{
            if (res.errorCode == 0) {
              // console.log(res);
              vm.productDetail = new Object (res.data);
              console.log(vm.productDetail);
              }
        })
      },
      //获取当前商品的热销推荐商品
      productHotList (productId, hot, pagesize) {
        var vm = this
        vm.$get (vm.API.API_URL_CATALOG_PRODUCTS_HOT + '/' + productId + '/hot/' + hot + '/pagesize/' + pagesize).then(res =>{
            if (res.errorCode == 0) {
              vm.productHots = res.data
            }
        })
      },
      //获取商品库存
      getSkuList (productId) {
        var vm = this
        vm.$get(vm.API.API_URL_CATALOG_PRODUCT_SKU + '/' + productId).then(res =>{
            if (res.errorCode == 0 && res.data) {
              vm.skuList = res.data
              if (vm.skuList) {
                vm.productOptionList (productId)
              }
            }
        })
      },
      //获取商品包装类型
      productOptionList (productId) {
        var vm = this
        vm.$get (vm.API.API_URL_CATALOG_PRODUCT_OPTION + '/' + productId).then(res =>{
            if (res.errorCode == 0) {
              var optionShow = []
              var optionList = res.data
              if (res.data) {
                for (var item in res.data) {
                  for (var option in res.data[item].optionValuesList) {
                    for (var sku in vm.skuList) {
                      var optionValueKeyGroup = vm.skuList[sku].optionValueKeyGroup.split ('_')
                      for (var optionGItem in optionValueKeyGroup) {
                        var optionId = res.data[item].optionValuesList[option].id
                        if (optionId == optionValueKeyGroup[optionGItem]) {
                          optionShow.push (res.data[item].optionValuesList[option])
                        }
                      }
                    }
                  }
                }
                for (var option in optionList) {
                  optionList[option].optionValuesList = optionShow
                }
                vm.optionList = optionList
              }
            }
        })
      },
      //监听包装类型选项
      optionSelected (optionId) {
        this.optionSelectId = optionId
        if (this.skuList) {
          for (var sku in this.skuList) {
            var optionValueKeyGroup = this.skuList[sku].optionValueKeyGroup.split ('_')
            for (var optionGItem in optionValueKeyGroup) {
              if (optionId == optionValueKeyGroup[optionGItem]) {
                this.productDetail.shopPrice = this.skuList[sku].price
                this.productDetail.quantity = this.skuList[sku].quantity
                this.params.optionValueKeyGroup = this.skuList[sku].optionValueKeyGroup
              }
            }
          }
        }
      },
      //加载商品评论
      loadreviewsList (productId) {
        var vm = this
        vm.$get(vm.API.API_URL_CATALOG_SHOW_REVIEW + '/' + productId).then(res =>{
          if (res.errorCode == 0) {
              vm.reviewsList = res.data
           }
        })
      },
      //商品详情、评论选项
      tabSelect () {
        this.tabSelected = !this.tabSelected
      },
      getDetail (productId) {
        this.productDetailList (productId)
        this.productHotList (productId, 1, 3)
        this.getSkuList (productId)
        this.loadreviewsList (productId)
        var params = {
          id: productId
        }
        this.Storage.Session.set ("data", params)
      },
      submit (flag) {
        var vm = this
        var user = this.Storage.Session.get ('User')
        if (user) {
          if (this.getCartCount () >= 15) {
            this.Toastr.warning ("购物车已达上限!")
            return
          } else {
            this.params.userId = user.id
            this.cartOrOrder (flag)
          }
        } else {
          vm.Toastr.warning ("请先登录再操作!")
		  vm.$router.push({
            path: '/login',
            name: 'Login'
          })
        }
      },
      //加入购物车、立即购买
      cartOrOrder (flag) {
        var vm = this
        if (this.optionList.length != 0) {
          if (this.params.optionValueKeyGroup) {
            this.params.skuId = this.getSku (this.params.optionValueKeyGroup)
            this.params.jsonData = this.getJsonData (this.params.optionValueKeyGroup)
          } else {
            this.Toastr.warning ("您选择的类别没有库存!")
            return
          }
        }
        for (var key in this.skuList) {
          if (this.skuList[key].optionValueKeyGroup == this.params.optionValueKeyGroup) {
            if (this.skuList[key].quantity < this.params.quantity) {
              this.Toastr.warning ("库存不足!")
              return
            }
          }
        }
        vm.$post(vm.API.API_URL_CART_ADD,vm.params).then(res =>{
         if (res.errorCode == 0 && res.errorMsg == "成功") {
              if (flag == 1) {
                vm.getCartCount () 
                vm.Toastr.success ("加入购物成功!")
              } else {
                vm.$router.push ({
                  path: '/account/cart',
                  name: 'Cart'
                })
              }
              vm.$store.commit ('setCartCount', vm.getCartCount ())
            } else {
              vm.Toastr.warning ("加入购物车失败!")
            }
        })
      },
      //获取购物车数量
      getCartCount () {
        var vm = this
        var user = this.Storage.Session.get ("User")
        var cartCount = 0
        if (user) {
          var userId = user.id
          vm.$post (vm.API.API_URL_CART_COUNT,{
              'userId': userId
            }).then(res =>{
             if (res.errorCode == 0 && res.data != null) {
                vm.cartCount = res.data
                cartCount = res.data
             }
          })
        }
        return cartCount
      },
      //添加收藏
      addWish (productId) {
        var vm = this
        var user = vm.Storage.Session.get ('User')
        if (user && productId) {
          var userId = user.id
          vm.$post(vm.API.API_URL_CUSTOMER_ADD_WISHLIST,{
              userId: userId,
              productId: productId
            }).then(res =>{
              if (res.errorCode == 0 && res.data) {
                if (res.data.success) vm.Toastr.success ("添加成功！")
                else vm.Toastr.error ("请勿重复添加！")
              } else {
                vm.Toastr.error ("添加失败！")
              }
            })
        } else {
          vm.Toastr.warning ("请先登录再收藏商品!")
        }
      },
      //添加评论
      addReviews () {
        var vm = this
        var user = vm.Storage.Session.get ('User')
        if (user) {
          if (vm.reviews.content.length == 0) {
            vm.Toastr.warning ("还没填写评论呢!")
          }else if (vm.reviews.stars == 0) {
            vm.Toastr.warning ("请勾选星级!")
          } else {
            vm.reviews.userId = user.id
            vm.$post (vm.API.API_URL_CATALOG_ADD_REVIEW,vm.reviews).then(res =>{
              if (res.errorCode == 0 && res.data) {
                  vm.Toastr.success ("添加评论成功,等待审核!")
                  this.loadreviewsList (vm.reviews.productId)
                  vm.reviews.content = ''
                  vm.reviews.stars = ''
                } else {
                  vm.Toastr.success ("添加评论失败!")
                }
             })
          }
        } else {
          vm.Toastr.warning ("添加评论失败,请登录!")
        }
      },
      getSku (optionValueKeyGroup) {
        if (this.skuList != null) {
          for (var index in this.skuList) {
            if (this.skuList[index].optionValueKeyGroup == optionValueKeyGroup) {
              return this.skuList[index].id
            }
          }
        }
        return null
      },
      getJsonData (optionValueKeyGroup) {
        if (this.skuList != null) {
          for (var index in this.skuList) {
            if (this.skuList[index].optionValueKeyGroup == optionValueKeyGroup) {
              return this.skuList[index].skuJson
            }
          }
        }
        return null
      }
    },
    watch: {
      getCartProductId: function (newVal, oldVal) {
        if (newVal) {
          this.productDetailList (newVal)
          this.productHotList (newVal, 1, 3)
          this.productOptionList (newVal)
          this.loadreviewsList (newVal)
          this.reviews.productId = newVal
        }
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  #qrcode {
    width: 50px;
    height: 50px;
    position: absolute;
    top: -6px;
    right: 70px;
  }
  .optionGroup {
    padding: 10px;
  }

  .option {
    padding: 4px;
    border: 1px solid #8ab933;
    border-radius: 3px;
    margin: 2px;
    cursor: pointer;
  }

  .optionSelected {
    background-color: #8ab933;
    color: #fff;
  }

  .quantityBox > .input-group {
    width: 100px;
  }
</style>