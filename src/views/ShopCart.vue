<template>
  <div>
    <!--header start-->
    <div style="width: auto;background: #e2e2e2;;padding: 5px;padding-right: 10%;padding-left: 10%">
      <a href="/" style="margin: 30px">首页</a>
      <a href="/myOrder">我的订单</a>
    </div>
    <!--header end-->
    <div class="main-body">
      <!--top start-->
      <div class="flex-div">
        <!--logo start-->
        <div class="logo-div flex-div">
          <img class="logo" src="../assets/small.png">
          <span>购物车</span>

        </div>
        <!--logo end-->
        <!--search start-->
        <div class="" style="width:70%;text-align: end;">
          <el-input placeholder="请输入内容" v-model="input5" class="input-with-select">
            <el-button type="danger" slot="append" icon="el-icon-search" style="background: #f64d4d;color: white"/>
          </el-input>
        </div>
        <!--search end-->
      </div>
      <!--top end-->
      <!--cart if start-->
      <div v-if="shopCart.length>0" style="border: 1px solid #c7c7c7;padding: 10px;margin-top: 10px;margin-bottom: 10px">
        <div v-if="this.shopCart.length>0">
          <el-row :gutter="20">
            <el-col :span="6" v-for="item in shopCart" :key="item.id" class="card_background">
              <div @click="toInfo(item.id)">
                <el-card :body-style="{ padding: '1px' }" class="product_card">
                  <img :src=item.url class="image">
                  <div class="card-outter">
                    <div class="card-inner">
                      <span style="overflow: hidden;text-overflow: ellipsis;white-space: nowrap">{{item.name}}</span>
                      <p>库存：{{item.stock}}</p>
                      <p>商铺：{{item.shopName}}</p>
                    </div>
                    <p style="color: #FF0000;height: 20%">￥{{item.price}}</p>
                  </div>
                </el-card>
              </div>


            </el-col>
          </el-row>
        </div>
      </div>
      <div v-else style="text-align: center;padding: 10%">
        <p>购物车内空空如也哦~</p><a href="/">去购物</a>
      </div>
      <!--cart if end-->

      <!--推荐-->
      <div>
        <div class="recommend-div">
          您可能需要：
        </div>
        <div v-if="this.products.length>0">
          <el-row :gutter="20">
            <el-col :span="6" v-for="item in products" :key="item.id" class="card_background">
              <div @click="toInfo(item.id)">
                <el-card :body-style="{ padding: '1px' }" class="product_card">
                  <img :src=item.url class="image">
                  <div class="card-outter">
                    <div class="card-inner">
                      <span style="overflow: hidden;text-overflow: ellipsis;white-space: nowrap">{{item.name}}</span>
                      <p>库存：{{item.stock}}</p>
                      <p>商铺：{{item.shopName}}</p>
                    </div>
                    <p style="color: #FF0000;height: 20%">￥{{item.price}}</p>
                  </div>
                </el-card>
              </div>


            </el-col>
          </el-row>
        </div>
      </div>
    </div>
  </div>

</template>

<script>
  export default {
    name: 'ShopCart',
    data () {
      return {
        input5: '',
        shopCart: [],
        products: [],  //商品s
      }
    },
    methods: {
      //获取商品
      getProducts () {
        let params = {
          pageNo: 1,
          pageSize: 10
        }

        params.typeId = this.products.typeId

        this.$axios.get('/api/customer/getProductList', {
          params: params
        }).then(res => {
          console.log(res)
          if (res.data.status && res.data.status === 'success') {
            if (this.load) {
              if (res.data.data.length > 0) {
                for (const i in res.data.data) {
                  this.products.push(res.data.data[i])
                }
              }
            } else {
              this.products = res.data.data
            }
          } else {
            this.$message.error(res.data.data.errMsg)
          }
        }).catch(err => {
          this.$message.error('Unknown error....')
        })
      },
      //跳转商品信息
      toInfo (id) {
        this.$router.replace({
          query: {
            productId: id
          },
          path: '/productInfo'
        })

        // this.$router.go(0)

        this.getProductDetail()

        document.getElementById('top').scrollIntoView()

        // this.$router.push('productInfo')
      },
      getShopCart () {
        this.$axios.get('/api/customer/getShopCart')
          .then(res => {
            console.log(res)
            if (res.data.status === 'success') {
              this.shopCart = res.data.data
            } else {
              this.$message.error(res.data.data.errMsg)
            }
            this.getProducts()
          }).catch(err => {
          this.$message.error('Unknown error')
        })
      }
    },
    created () {
      this.getShopCart()
    }
  }
</script>

<style scoped>
  .main-body {
    padding-top: 2%;
    margin-left: 10%;
    margin-right: 10%;
    min-width: 1000px;
  }

  .logo {
    width: 160px;
    height: 90px;
  }

  .flex-div {
    display: flex;
  }

  .logo-div {
    width: 300px;
  }

  span {
    font-size: 28px;
    color: #f64d4d;
    margin-top: 10%;
  }

  .el-select .el-input {
    width: 130px;

  }

  .input-with-select {
    width: auto;
    margin-top: 3%;
  }

  .input-with-select .el-input-group__prepend {
    background-color: #fff;
    width: auto;
  }

  .product_card {
    cursor: pointer;
  }

  .product_card:hover {
    box-shadow: -7px 0 5px -5px #333;
    background: #f84e4e;
    color: #FFFFFF;
  }

  .card_background:hover {
    background: #ffffff;
  }

  .image {
    width: 99%;
    height: 140px;
    margin: 2px;
    display: block;
  }

  .recommend-div {
    height: 40px;
    text-align: start;
    background: #f64d4d;
    padding-top: 10px;
    padding-left: 5px;
    color: #FFFFFF;
  }
  a:hover{
    color: #f64d4d;

  }
</style>
