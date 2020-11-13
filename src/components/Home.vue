<template>
  <div id="app-content">
    <section>
      <el-carousel id="BannerWrap" indicator-position="outside">
        <el-carousel-item v-for="(ImgItem,index) in bannerlist" :key="index">
            <!-- <img :src="API.BASE_SERVER_URL+ImgItem.image" @click="letsgo(ImgItem.url)"/> -->
            <img :src="API.BASE_SERVER_URL+ImgItem.image" :title="ImgItem.url"/>
        </el-carousel-item>
      </el-carousel>
      <div class="div" style="width: 20vw;position: relative;float: left; top: 0; z-index: 99;">
        <el-menu default-active="2">
          <el-menu-item index="2" @click="goto('/category','Category',8)">
            <i class="el-icon-menu"></i>
            <span slot="title">分类</span>
          </el-menu-item>
          <el-menu-item
            index="3"
            v-if="index < 15"
            @click="goto('/category','Category',category.id)"
            v-for="(category,index) in
          categoryList"
            v-bind:key="category.id"
          >
            <i class="el-icon-location"></i>
            <span slot="title">{{category.name}}</span>
          </el-menu-item>
          <el-menu-item
            index="4"
            @click="goto('/categories','Categories')"
            v-if="categoryList.length >= 15"
          >
            <i class="el-icon-more"></i>
            <span slot="title">查看更多分类</span>
          </el-menu-item>
        </el-menu>
      </div>

      <div class="container">
        <div id="page"></div>
        <div class="row">
          <div class="col-lg-9 col-md-9 col-sm-9 col-lg-push-3 col-md-push-3 col-sm-push-3" style="left: 0;">
            <template v-for="(category,index) in categoryRelationList">
              {{category.name}}
              <div class="divider divider-border divider-center" v-bind:key="index">
                <!-- divider -->
                <i class="fa fa-users"></i>
              </div>
              <ul class="shop-item-list row list-inline nomargin" v-bind:key="category.name">
                <!-- 产品-->
                <li
                  class="col-lg-3 col-sm-3"
                  v-for="product in category.productRelationList"
                  v-bind:key="product.id"
                >
                  <div class="shop-item">
                    <div class="thumbnail" style="position: relative;">
                      <!-- product image(s) -->
                      <!-- <a @click="goto('/product/','Product',product.id)" class="shop-item-image"> -->
                      <a @click="$router.push('/product/'+product.id)" class="shop-item-image">
                        <img
                          class="img-responsive"
                          v-bind:src="API.BASE_SERVER_URL+product.defaultImg"
                        >
                      </a>
                      <!-- /product image(s) -->
                      <!-- hover buttons -->
                      <span class="itemMask" style="background: rgba(0,0,0,0.2);width:100%;height: 30%;position: absolute; left:0;bottom: 0;z-index: 20;
  text-align: center;padding-top: 5%">
                        <el-button type="danger" @click="addWish(product.id)" plain icon="el-icon-star-on" circle></el-button>
                      </span>
                      <!-- /hover buttons -->
                      <!-- product more info -->
                      <div class="shop-item-info"></div>
                      <!-- /product more info -->
                    </div>
                    <div class="shop-item-summary text-center">
                      <h2>{{product.name}}</h2>
                      <!-- price -->
                      <div class="shop-item-price">
                        <span class="line-through">￥{{product.price}}</span>
                        ￥{{product.shopPrice}}
                      </div>
                      <!-- /price -->
                    </div>
                    <!-- /buttons -->
                  </div>
                </li>
                <!-- /产品 -->
              </ul>
            </template>
            <hr>
            <!-- Pagination Default -->
            <div name="bootpagFooter" class="text-center"></div>
            <!-- /Pagination Default -->
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: "Home",
  data() {
    return {
      isMask: false,
      categoryList: [],
      categoryRelationList: [],
      bannerlist: [
        {imgsrc: require('../../static/images/1.png')},
        {imgsrc: require('../../static/images/2.png')},
        {imgsrc: require('../../static/images/3.png')}
      ]
    };
  },
  created: function() {
    this.loadIndexData();
    this.DogExist();
  },
  methods: {
    DogExist() {
      // var vm = this;
      // this.$get(this.API.API_URL_Dongle).then(res => {
      //   if(res.errorCode == 0) {
      //     vm.Toastr.success("加密狗正常！");
      //   } else {
      //     vm.Toastr.error("加密狗无效，请检查");
      //   }
      // })
    },
    //添加商品收藏
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
                if (res.data.success) vm.Toastr.success ("收藏成功！")
                else vm.Toastr.error ("请勿重复收藏！")
              } else {
                vm.Toastr.error ("收藏失败！")
              }
            })
        } else {
          vm.Toastr.warning ("请先登录再收藏商品!")
        }
      },
    loadIndexData() {
      var vm = this;
      vm.$get(vm.API.API_URL_CATEGORY_CONDITION).then(res => {
        if (res.errorCode == 0) {
          vm.categoryList = res.data;
        }
      });
      vm.$post(vm.API.API_URL_CATALOG_INDEX_PRODUCTS + "/8").then(res => {
        if (res.errorCode == 0) {
          vm.categoryRelationList = res.data;
        }
      });
      vm.$post(vm.API.API_URL_ADVERT).then(res => {
        if (res.errorCode == 0) {
          vm.bannerlist = res.data;
        }
      });
    },
    goto(path, name, val) {
      // var user = this.Storage.Session.get("User");
      var vm = this;
      // var url = path+val
      console.log(url);
      this.$router.push({
        path: '/product/'+933,
        name: name
      });
      var params = {
        id: val
      };
      // this.$router.push(url);
      this.Storage.Session.set("data", params);
    },
    letsgo(index){
      window.location.href = 'http://'+index; 
    }
  }
};
</script>

<style scoped>
.loading-div {
  position: relative;
}

.loading-img {
  position: absolute;
  top: 50%;
  left: 50%;
}
#BannerWrap{
  width: 80%;
  margin: 0 auto;
}
#BannerWrap img{
  width: 100%; height: 100%;
}
.itemMask {
  display: none;
}
.thumbnail:hover  .itemMask {
    display: inline-block;
}
</style>
