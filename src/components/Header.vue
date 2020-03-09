<template>
  <div id="top" style="min-width: 1000px">
    <div style="display: flex">
      <!--名称 start-->
      <div style="text-align: center;width: 33%;display: flex">
        <img style="margin-top: 5px;height: 30px;width: 50px;margin-left: 25%" :src="logo" alt="">
        <H2 style="text-align: center;color: #f84e4e">小商品</H2>
      </div>
      <!--名称 end-->
      <!--搜索框 start-->
      <div style="text-align: center;width: 34%">
        <el-input placeholder="搜索商品名、商品描述" v-model="searchKey" class="input-with-select">
          <el-button style="color: #ffffff;background: #f84e4e" slot="append" icon="el-icon-search" @click="search">搜索
          </el-button>
        </el-input>
        <!--        <input type="text" placeholder="输入你想查询的内容" class="g_input_search">-->
      </div>
      <!--搜索框 end-->
      <!--登录/注册按钮 start-->
      <div style="text-align: center;margin-left:10%;width: 15%;display: flex">
        <a href="/login" v-show="!isLogin" type="info" plain>登录</a>
        <a v-show="!isLogin" type="danger" plain>注册</a>
        <a href="#" v-show="isLogin" style="margin-top: 2%;text-align: center">{{userName}}</a>
        <a href="/shopCart" v-show="isLogin"
           style="margin-left: 20px;margin-top: 2%;text-align: center">购物车</a>
        <a href="/#" v-show="isLogin"
           @click="quit"
           style="margin-left: 20px;margin-top: 2%;text-align: center">退出</a>
      </div>
      <!--登录/注册按钮 end-->
    </div>
    <!--menu start-->
    <div style="height: 15%;background:#f84e4e;margin-top: 10px;display: flex">
      <!--向左 start-->
      <div style="width: 10%;text-align: center">
        <div style="width: 40px;height: 40px;">
        </div>
      </div>
      <!--向左 end-->
      <!--中间主菜单 start-->
      <div style="min-width: 70%;max-width:70%;display: flex">
        <div v-for="item in menu" :key="item.id" style="width: 160px;height: 40px;padding-top: 15px"
             class="menu_item menu_it btn">
          <el-dropdown v-if="item.child" size="medium" placement="bottom">
            <span class="el-dropdown-link" style="color: #FFF">
              {{item.title}}
              <i class="el-icon-arrow-down el-icon--right"/>
            </span>
            <el-dropdown-menu v-show="item.child" slot="dropdown">
              <el-dropdown-item v-for="child in item.child" :key="child.id"
                                style="width: 100px;text-align: center">
                <span class="drop_item" @click="menuClick(child.id)">{{child.name}}</span>
              </el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
          <div v-else @click="menuClick(0)">
            {{item.title}}
          </div>
        </div>
      </div>

      <!--中间主菜单 end-->
      <!--向右 start-->
      <div style="width: 10%">
        <div style="width: 40px;height: 40px;">
        </div>
      </div>
      <!--向右 end-->
    </div>
    <!--menu end-->
  </div>
</template>

<script>
  import bus from '../assets/js/bus.js'

  export default {
    name: 'Header',
    data () {
      return {
        searchKey: '',
        menu: [
          {
            id: 0,
            title: '首页',
            path: '/index'
          }
        ],
        userName: '',
        logo: require('../assets/small.png'),
        isLogin: true
      }
    },
    methods: {
      //获取菜单栏数据
      getProductTypes () {
        this.$axios.get('/api/customer/getProductTypes')
          .then(res => {
            console.log(res)
            if (res.data.status === 'success') {
              let data = res.data.data
              for (const i in data) {
                this.menu.push(data[i])
              }
              console.log(this.menu)

            } else {
              this.$message.error(data.errMsg)
            }
          })
          .catch(err => {
            this.$message.error('unknown error...')
          })
      },
      menuClick (id) {
        if (id !== 0) {
          this.$router.push({
            query: {
              typeId: id
            },
            path: '/'
          })
          // this.$router.go(0)
        } else {
          this.$router.push('/')
          // this.$router.go(0)
        }
        bus.$emit('menu-click', true)
      },
      search () {
        this.$router.push({
          query: {
            search: this.searchKey
          },
          path: '/index'
        })
        bus.$emit('search', {
          key: true,
          value: this.searchKey
        })
      },
      quit () {
        sessionStorage.removeItem('isLogin')
        sessionStorage.removeItem('userName')
        this.$axios.get('/api/customer/logout')
        this.$router.replace('/login')
      }
    },
    activated () {
      this.getProductTypes()
      this.isLogin = sessionStorage.getItem('isLogin')
      this.userName = sessionStorage.getItem('userName')
    },
    created () {
      this.getProductTypes()
      this.isLogin = sessionStorage.getItem('isLogin')
      this.userName = sessionStorage.getItem('userName')
    }
  }
</script>

<style scoped rel="stylesheet/scss" lang="scss">
  .menu_item {
    min-height: 40px;
    padding-top: 10px;
    text-align: center;
    cursor: pointer;
    color: #FFFFFF;
  }

  .menu_it:hover {
    background: #ea0030;
  }

  .drop_item:hover {
    color: #f84e4e;
  }

  .g-container {
    position: relative;
    margin: 100px auto;
    display: flex;
    flex-wrap: wrap;
    width: 500px;
    height: 60px;
    overflow: hidden;
    transition: 0.3s;

    & > * {
      border: none;
      outline: none;
    }

    .g_input_search {
      padding: 0 15px;
      height: 100%;
      width: 100%;
      border: 1px solid #ddd;
      font-size: 18px;
      box-sizing: border-box;

      &:not(:placeholder-shown) {
        border: 1px solid #03a9f4;

        + .g_button_search {
          opacity: 1;
        }
      }

      &:placeholder-shown {

        + .g_button_search {
          opacity: 0;
        }
      }
    }
  }

  a:hover {
    color: #f64d4d;
  }

</style>
