<template>
  <div id="top" class="my-main-body">
    <!--商品信息 start-->
    <div class="product-info">
      <!--左边图片 start-->
      <div class="left_div">
        <div class="left-inner">
          <el-carousel class="my-el-carousel" :autoplay="false">
            <el-carousel-item class="my-el-carousel-item" v-for="item in productDetail.images" :key="item.id">
              <div>
                <pic-zoom :url="item.url">
                </pic-zoom>
              </div>

            </el-carousel-item>
          </el-carousel>
        </div>

      </div>
      <!--左边图片 end-->
      <!--右边购买 start-->
      <div class="right_div">
        <div class="product-name">
          {{productDetail.name}}
        </div>
        <!--价格 start-->
        <div class="price-div-1">
          <div class="price-div-2">价格：</div>
          <div class="price-div-3">
            <div class="price-div-4">￥</div>
            <div class="price-div-5">
              <b>{{productDetail.price}}</b>
            </div>
          </div>
        </div>
        <!--价格 end-->
        <!--好评数量+销售量 start-->
        <div class="cnt-div-1">
          <div class="cnt-div-2">
            好评个数：{{productDetail.likeCnt}}
          </div>
          |
          <div class="cnt-div-2">
            销售量：{{productDetail.saleCnt}}
          </div>
          |
          <div class="cnt-div-2">
            库存：{{productDetail.stock}}
          </div>
        </div>
        <!--好评数量+销售量 start-->
        <!--简介 start-->
        <div
          class="desc_div">
          简介： {{productDetail.desc}}
        </div>
        <!--简介 end-->
        <!--选择数量 start-->
        <div class="select-div">
          <el-input-number size="mini" v-model="cnt" :min="1" :max='productDetail.stock'/>
        </div>
        <!--选择数量 end-->
        <!--购买按键 start-->
        <div class="buy-div">
          <el-button type="danger" plain class="buy-button" @click="buy">立即购买</el-button>
          <el-button type="danger" class="buy-button" @click="addToCart">加入购物车</el-button>
        </div>
        <!--购买按键 end-->
        <div class="simple-txt-div">
          <div class="txt-1">服务承诺</div>
          <div class="txt-2">正品保证</div>
          <div class="txt-2">极速退款</div>
          <div class="txt-2">七天无理由退换</div>
        </div>
      </div>
      <!--右边购买 end-->
    </div>
    <!--商品信息 end-->
    <!--分割线 start-->
    <div class="divide"/>
    <!--分割线 end-->
    <!--评论 start-->
    <div class="comment">
      <div
        class="comment-label">
        累计评论
      </div>
      <div v-if="productDetail.comments.list.length>0">
        <div v-for="item in productDetail.comments.list" class="comment-item">
          <div class="comment-username">
            <div>
              {{item.userName}}
            </div>
            <p class="comment-content">:</p>
            <div style="width: 90%;text-align: start">
              {{item.comment}}
            </div>
            <div>
              <el-checkbox v-model="checked"/>
            </div>
          </div>

          <hr/>
        </div>
        <el-pagination
          small
          @current-change='pageChange'
          :current-page.sync="currentPage"
          layout="prev, pager, next"
          :total="productDetail.comments.pageRows">
        </el-pagination>
      </div>
      <div class="comment-else" v-else>
        暂时没有评论耶~
      </div>

    </div>
    <!--评论 end-->
    <!--推荐-->
    <div style="padding-right: 14.5%;padding-left: 15%">
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

    <!--付款弹窗 start-->
    <el-dialog
      title="付款"
      :visible.sync="dialogVisible"
      width="30%"
      center>
      <p style="font-size: 10px;color: #FF0000">申请支付宝SDK步骤麻烦，先省略</p>
      <img src="../assets/pay.jpg" style="height: 450px;width: 380px" alt="">
      <span slot="footer" class="dialog-footer">
    <el-button @click="dialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="confirmBuy">确 定</el-button>
  </span>
    </el-dialog>
    <!--付款弹窗 end-->
  </div>
</template>

<script>
  import PicZoom from 'vue-piczoom'

  export default {
    name: 'ProductInfo',
    components: {
      PicZoom
    },
    data () {
      return {
        productDetail: {
          comments: {
            list: []
          }
        },
        cnt: 1,
        commentPageNo: 2,
        commentPageSize: 10,
        currentPage: 1,
        checked: false,
        products: [],  //商品s
        dialogVisible: false
      }
    },
    methods: {
      getProductDetail () {
        let productId = this.$route.query.productId
        this.$axios.get('/api/customer/getProductDetail', {
          params: {
            productId: productId
          }
        }).then(res => {
          console.log(res)
          this.productDetail = res.data.data
          this.getProducts()

        })
      },
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
      pageChange () {
        this.$axios.get('/api/customer/getCommentByProductId', {
          params: {
            productId: this.productDetail.id,
            pageNo: this.currentPage,
            pageSize: this.commentPageSize
          }
        }).then(res => {
          if (res.data.status === 'success') {
            this.productDetail.comments.list.push(res.data.data.list)
          } else {
            this.$message.error(res.data.data.errMsg)
          }
        }).catch(err => {
          this.$message.error('Unknown error')
        })
      },
      addToCart () {
        this.$axios.get('/api/customer/addToCart', {
          params: {
            productId: this.productDetail.id
          }
        }).then(res => {
          if (res.data.status === 'success') {
            this.$message.success('已加入')
          } else {
            this.$message.info(res.data.data.errMsg)
          }
        }).catch(err => {
          this.$message.error('Unknown error')
        })
      },
      buy () {
        this.dialogVisible = true
      },
      confirmBuy () {
        let params = new URLSearchParams()
        params.append('productId', this.productDetail.id)
        params.append('buyCnt', this.cnt)
        this.$axios(
          {
            method: 'post',
            url: '/api/customer/buyProduct',
            data: params
          }
        ).then(res => {
          if (res.data.status === 'success') {
            this.$message.success('下单成功')
            this.$router.go(0)
          } else {
            this.$message.error(res.data.data.errMsg)
          }
        }).catch(err => {

        })

      }
    },
    created () {
      this.getProductDetail()
    }
  }
</script>

<style scoped>
  .left_div {
    width: 50%;
    height: 500px;
  }

  .right_div {
    width: 50%;
    height: 50%;
  }

  /*.image_hover:hover {*/
  /*  !*position: absolute;*!*/
  /*  !*width: 100%;*!*/
  /*}*/
  .desc_div {
    margin-left: 5%;
    text-align: left;
    padding-right: 5%;
    padding-left: 5%;
    color: #858585;
    border: 1px solid #b9b9b9;
    height: 200px;
    overflow: scroll
  }

  .desc_div::-webkit-scrollbar {
    width: 0 !important
  }

  .my-main-body {
    background: #ffffff;
    margin-top: 1%;
    min-width: 1000px;
    min-height: 100%
  }

  .product-info {
    margin-top: 60px;
    display: flex;
    width: 70%;
    padding-right: 15%;
    padding-left: 15%;
  }

  .left-inner {
    margin: 2px;
    background: #FFFFFF;
    border: medium solid #d5d5d5;
    min-height: 500px
  }

  .my-el-carousel {
    min-height: 500px
  }

  .my-el-carousel-item {
    min-height: 500px
  }

  .product-name {
    padding: 10px;
    margin-left: 5%;
    font-size: 24px;
    text-align: left;
    border: 1px solid #ff3c3f;
  }

  .price-div-1 {
    background: #e2e2e2;
    display: flex;
    text-align: center;
    margin-left: 5%;
    padding: 10px
  }

  .price-div-2 {
    margin-left: 5%;
    margin-top: 1%;
    color: #a6a6a6
  }

  .price-div-3 {
    color: red;
    display: flex
  }

  .price-div-4 {
    font-size: 20px;
    margin-top: 4px
  }

  .price-div-5 {
    font-size: 24px;
    font-family: 微软雅黑
  }

  .cnt-div-1 {
    display: flex;
    padding-left: 5%;
    padding-right: 5%;
    margin: 5px;
    color: #a6a6a6
  }

  .cnt-div-2 {
    width: 30%;
    margin-left: 3px
  }

  .select-div {
    margin-top: 10px;
    text-align: start;
    padding-left: 10%
  }

  .buy-div {
    text-align: center;
    display: flex
  }

  .buy-button {
    margin: 5%;
    width: 45%
  }

  .simple-txt-div {
    color: #b9b9b9;
    display: flex
  }

  .txt-1 {
    margin-left: 5%;
  }

  .txt-2 {
    margin-left: 5%;
    color: #575757
  }

  .recommend-div {
    height: 40px;
    text-align: start;
    background: #f64d4d;
    padding-top: 10px;
    padding-left: 5px;
    color: #FFFFFF;
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
    width: 100%;
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

  .divide {
    width: 70%;
    background: #d4d4d4;
    height: 2px;
    margin-top: 2%;
    margin-left: 15%;
    margin-right: 15%
  }

  .comment {
    margin-top: 1%;
    min-width: 1000px;
    min-height: 100%;
    padding-left: 15%;
    padding-right: 15%
  }

  .comment-label {
    width: 100%;
    text-align: start;
    font-size: 20px;
    background: #f84e4e;
    padding: 5px 5px 10px;
    color: #FFFFFF
  }

  .comment-item {
    text-align: start;
    padding: 5px
  }

  .comment-username {
    padding: 10px;
    display: flex
  }

  .comment-content {
    margin-right: 5px;
    margin-left: 5px
  }

  .comment-else {
    color: #d4d4d4;
    font-size: 26px
  }

  .card_background:hover {
    background: #ffffff;
  }

  .product_card {
    cursor: pointer;
  }

  .product_card:hover {
    box-shadow: -7px 0 5px -5px #333;
    background: #f84e4e;
    color: #FFFFFF;
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

  .image {
    width: 99%;
    height: 140px;
    margin: 2px;
    display: block;
  }
</style>
