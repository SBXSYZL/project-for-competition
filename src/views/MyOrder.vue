<template>
  <div style="width:auto">
    <!--header start-->
    <div style="width: auto;background: #e2e2e2;;padding: 5px;padding-right: 10%;padding-left: 10%">
      <a href="/" style="margin: 30px">首页</a>
      <a href="/shopCart">购物车</a>
    </div>
    <!--header end-->
    <div style="width:80%;margin-left: 10%;margin-right: 10%;background:white">
      <div style="margin-top: 10px">
        <span style="font-size: 24px;color: #f64d4d">我的订单</span>
      </div>
      <div v-if="orders.length>0" style="border: black 1px solid;padding: 20px">
        <table style="width: 100%">
          <tr v-for="(item,index) in orders" :key="index"
              style="border: 1px black solid;">
            <td>
              <div style="background: #e8e8e8;padding: 5px">
                <span>订单号:{{item.id}}</span> <span>状态: {{item.status===0?'等待送达':'配送完成'}}</span>
              </div>
              <div style="width: auto;display: flex">
                <img :src="item.url" style="width: 100px;height: 100px">
                <div style="width: 100%;display: flex">
                  <div style="width: 90%;color: #a3a3a3;padding-left: 10px">
                    <span>商品：{{item.productName}} </span><br>
                    <span>金额：{{item.total}}</span><br>
                    <span>单价：{{item.price}} </span>
                    <span>数量：{{item.quantity}}</span><br>
                    <span>付款人：{{item.userName}}</span>
                  </div>
                  <div>
                    <a v-show="item.status!==0" style="margin: 0 auto" @click="readyComment(item.productId)">评价</a>
                  </div>

                </div>
              </div>

              <hr>
            </td>
          </tr>
        </table>
      </div>
      <div v-else style="height: 100%;width: 100%">
        <p style="font-size: 24px;color: #a3a3a3;margin-left:45%;margin-top: 15%">
          空空如也耶~
        </p>

      </div>
    </div>
    <el-dialog
      title="评价"
      :visible.sync="dialogVisible"
      width="30%">
      <el-input v-model="input" placeholder="请输入评价内容"/>
      <span slot="footer" class="dialog-footer">
    <el-button @click="dialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="comment" style="background: #f64d4d">确 定</el-button>
  </span>
    </el-dialog>
  </div>
</template>

<script>
  export default {
    name: 'MyOrder',
    data () {
      return {
        orders: [],
        pageNo: 1,
        pageSize: 10,
        dialogVisible: false,
        input: '',
        productId: -1
      }
    },
    methods: {
      getOrders () {
        this.$axios.get('/api/customer/getMyOrders', {
          params: {
            pageNo: this.pageNo,
            pageSize: this.pageSize
          }
        }).then(res => {
          console.log(res)
          this.orders = res.data.data.list
        })
      },
      readyComment (id) {
        this.productId = id
        this.dialogVisible = true
      },
      comment () {
        if (this.productId !== -1) {
          let params = new URLSearchParams()
          params.append('comment', this.input)
          params.append('productId', this.productId)

          this.$axios({
            method: 'post',
            url: '/api/customer/comment',
            data: params
          }).then(res => {
            console.log(res)
            if (res.data.status === 'success') {
              this.$message.success('评价成功')
            } else {
              this.$message.error(res.data.data.errMsg)
            }
            this.dialogVisible = false
            this.productId = -1
          }).catch(err => {
            this.$message.error('Unknown error')
          })
        } else {
          this.$message.error('评价失败')
        }

      }
    },

    created () {
      this.getOrders()
    }

  }
</script>

<style scoped>
  a:hover {
    color: #f64d4d;
    cursor: pointer;
  }

  span {
    width: 300px;
  }
</style>
