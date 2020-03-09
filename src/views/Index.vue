<template>
  <div class="my-main-body">
    <!--轮播图 start-->
    <div class="marquee">
      <el-carousel indicator-position="outside">
        <el-carousel-item v-for="item in images" :key="item.id">
          <a href='#' class="a-st">
            <img class="marquee-image" :src=item.url alt="">
          </a>
        </el-carousel-item>
      </el-carousel>
    </div>
    <!--轮播图 end-->
    <div class="cards-box">
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
      <div v-else class="else-div">
        <p class="p-st">好可惜哦，没有相关产品~</p>
      </div>

      <div v-loading="true" class="loading-div" v-show="end">
        &nbsp;
      </div>
    </div>
    <div
      class="to-top"
      @click="toTop">
      <img class="to-top-img" :src='toTopImg' alt=""/>
    </div>

  </div>
</template>

<script>
  import bus from '../assets/js/bus.js'

  export default {
    name: 'Index',
    data () {
      return {
        images: [],   //轮播图
        toTopImg: require('../assets/to-top.png'),    //锚点图片
        end: false,    //标记是否滚到底
        typeId: -1,    //已选择类型
        shopId: -1,    //已选择商铺ID
        load: false,  //是否加载过了
        pageNo: 1,    //页码
        pageSize: 10, //请求量
        products: []  //商品s
      }
    },
    methods: {
      //请求随机轮播图片
      randomSelectImg () {
        this.$axios.get('/api/customer/randomSelectImg')
          .then(res => {
            if (res.data.status && res.data.status === 'success') {
              this.images = res.data.data
            } else {
              this.$message.error(res.data.data.errMsg)
            }
          })
      },
      //滚到顶部
      toTop () {
        document.getElementById('top').scrollIntoView()
      },
      //跳转商品信息
      toInfo (id) {
        this.$router.push({
          query: {
            productId: id
          },
          path: '/productInfo'
        })
        // this.$router.push('productInfo')
      },
      //获取商品
      getProducts () {
        let searchKey = this.$route.query.search
        if (searchKey) {
          this.$axios.get('/api/customer/searchProduct', {
            params: {
              searchKey: searchKey,
              pageNo: 1,
              pageSize: 10
            }
          }).then(res => {
            if (res.data.status === 'success') {
              this.products = res.data.data.list
            } else {
              this.$message.error(res.data.data.errMsg)
            }
          }).catch(err => {
            this.$message.error('Unknown error')
          })
        } else {
          let params = {
            pageNo: this.pageNo,
            pageSize: this.pageSize
          }
          if (this.typeId > 0) {
            params.typeId = this.typeId
          }
          if (this.shopId > 0) {
            params.shopId = this.params
          }
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
        }

      }
    },
    activated () {
      let id = this.$route.query.typeId
      if (id) {
        this.typeId = id
      } else {
        this.typeId = -1
      }
      this.randomSelectImg()
      this.getProducts()
    },

    created () {
      let id = this.$route.query.typeId
      if (id) {
        this.typeId = id
      } else {
        this.typeId = -1
      }
      this.randomSelectImg()
      this.getProducts()

    },
    mounted () {
      //监听滚动条
      bus.$on('end', (params) => {
        if (params) {
          // alert('b')
          //load more
          this.end = true
          if (!this.load) {
            this.pageNo = 2
            this.getProducts()
          }
          this.load = true

        } else {
          this.end = false
        }

      })

      bus.$on('menu-click', (params) => {
        if (params) {
          this.$router.go(0)
          bus.$emit('menu-click', false)
        }
      })

      bus.$on('search', (params) => {
        if (params.key) {
          console.log(params)
          bus.$emit('search', {
            key: false,
            value: params.value
          })
          this.getProducts()
        }else{

        }
      })
    }

  }
</script>

<style scoped rel="stylesheet/scss" lang="scss">
  .el-carousel__item h3 {
    color: #ffffff;
    font-size: 18px;
    opacity: 0.75;
    line-height: 300px;
    margin: 0;
  }

  .el-carousel__item:nth-child(2n) {
    background-color: #ffffff;
  }

  .el-carousel__item:nth-child(2n+1) {
    background-color: #ffffff;
  }

  .time {
    font-size: 13px;
    color: #999;
  }

  .bottom {
    margin-top: 13px;
    line-height: 12px;
  }

  .button {
    padding: 0;
    float: right;
  }

  .image {
    width: 99%;
    height: 140px;
    margin: 2px;
    display: block;
  }

  .clearfix:before,
  .clearfix:after {
    display: table;
    content: "";
  }

  .clearfix:after {
    clear: both
  }

  .to-top {
    position: fixed;
    left: 95%;
    top: 90%;
    width: 40px;
    height: 30px;
    float: right;
    margin-right: 30px
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

  .my-main-body {
    margin-top: 1%;
    min-width: 1000px;
    min-height: 100%
  }

  .marquee {
    width: 70%;
    margin-top: 20px;
    padding-right: 14%;
    padding-left: 15%;
    text-align: center
  }

  .a-st {
    width: 100%;
    height: 100%
  }

  .marquee-image {
    width: 100%;
    height: 100%
  }

  .cards-box {
    width: 70%;
    padding-right: 15%;
    padding-left: 15%;
  }

  .card-outter {
    text-align: start;
    min-height: 90px;
    padding: 5px
  }

  .card-inner {
    height: 80%;
    min-height: 60px
  }

  .else-div {
    padding-top: 20%
  }

  .p-st {
    font-size: 30px;
    color: #aeaeae
  }

  .loading-div {
    height: 10%;
    margin-top: 5%
  }

  .to-top-img {
    width: 30px;
    height: 30px
  }

  a:hover {
    color: #f64d4d;
  }
</style>
