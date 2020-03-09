<template>
  <div style="padding-top: 12%;background: white;height: 100%;width: 100%;display: flex;min-width: 1000px">
    <div style="width: 60%">
      <ul style="margin: 0px auto;list-style-type: none">
        <li v-for="item in imgS">
          <a href="/">
            <img class="left-img"
                 :src="item">
          </a>
        </li>
      </ul>
    </div>
    <div style="width: 40%">
      <div class="g-container">
        <h2>登录</h2>
        <el-form
          class="demo-ruleForm login-page"
          :model="myForm"
          ref="myForm"
          label-position="left"
          :rules="myRule"
          status-icon>
          <div class="g-username">
            <el-form-item>
              <el-input maxlength="64" v-model="myForm.tel" placeholder="请输入手机号" class="input"/>
            </el-form-item>
            <img src="https://b-gold-cdn.xitu.io/v3/static/img/greeting.1415c1c.png" class="g-username" alt="">
          </div>
          <div class="g-password">
            <el-form-item>
              <el-input type="password" v-model="myForm.password" maxlength="64" placeholder="请输入密码" class="input"/>
            </el-form-item>
            <img src="https://b-gold-cdn.xitu.io/v3/static/img/blindfold.58ce423.png" class="g-password" alt="">
          </div>

          <img src="https://b-gold-cdn.xitu.io/v3/static/img/normal.0447fe9.png" class="g-normal" alt="">

          <div style="width: 100%">
            <el-form-item>
              <el-button type="danger" style="width: 100%" @click="login">登 录</el-button>
            </el-form-item>

          </div>
          <div style="width: 100%;padding-top: 10px">
            <a href="/registered" style="color: #20a0ff">没有账号？注册一个</a>
          </div>
        </el-form>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'Login',
    data () {
      const validatePass = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入密码'))
        } else if (value.length < 6) {
          callback(new Error('密码长度最小6位'))
        } else if (value.length > 12) {
          callback(new Error('密码长度最大12位'))
        } else {
          callback()
        }
      }
      const validateTel = (rule, value, callback) => {
        if (value.length <= 0 || value.trim().length <= 0) {
          callback(new Error('请输入手机号'))
        } else if (value.length !== 11) {
          callback(new Error('手机号格式不正确'))
        } else {
          callback()
        }
      }
      return {
        imgS: [
          require('../assets/product1.jpg'),
          require('../assets/product2.jpg'),
          require('../assets/product3.jpg'),
          require('../assets/product4.jpg'),
          require('../assets/product5.jpg'),
          require('../assets/product6.jpg')
        ],
        myForm: {
          tel: '',
          password: ''
        },
        myRule: {
          tel: [{
            validator: validateTel,
            trigger: 'blur'
          }],
          password: [{
            validator: validatePass,
            trigger: 'blur'
          }]
        },

      }
    },
    methods: {
      login () {
        const that = this
        that.$refs.myForm.validate(valid => {
          if (valid) {
            let params = new URLSearchParams()
            params.append('tel', this.myForm.tel)
            params.append('password', this.myForm.password)
            this.$axios(
              {
                method: 'post',
                url: '/api/customer/login',
                data: params
              }
            ).then(res => {
              if (res.data.status === 'success') {
                sessionStorage.setItem('isLogin', true)
                sessionStorage.setItem('userName', res.data.data)
                this.$message.success('登录成功')
                this.$router.replace('/')
              } else {
                this.$message.error(res.data.data.errMsg)
              }
            }).catch(err => {
              this.$message.error('Unknown error')
            })
          } else {
            return false
          }
        })

      }
    }
  }
</script>

<style scoped rel="stylesheet/scss" lang="scss">
  body {
    padding: 0;
    margin: 0;
    overflow: hidden;
    height: 600px;
  }

  canvas {
    padding: 0;
    margin: 0;
  }

  div.btnbg {
    position: fixed;
    left: 0;
    top: 0;
  }

  $bg-normal: 'https://b-gold-cdn.xitu.io/v3/static/img/normal.0447fe9.png';
  $bg-username: 'https://b-gold-cdn.xitu.io/v3/static/img/greeting.1415c1c.png';
  $bg-password: 'https://b-gold-cdn.xitu.io/v3/static/img/blindfold.58ce423.png';

  .g-container {
    position: relative;
    width: 318px;
    margin: 100px auto;
    height: 370px;
    padding: 20px;
    box-sizing: border-box;
    background: #fff;
    z-index: 10;
    border: 1px solid black;

    h2 {
      font-size: 20px;
      font-weight: bold;
      margin-bottom: 30px;
    }

    input {
      outline: none;
      padding: 10px;
      width: 100%;
      border: 1px solid #e9e9e9;
      border-radius: 2px;
      outline: none;
      box-sizing: border-box;
      font-size: 16px;
    }
  }

  img {
    position: absolute;
    top: -20%;
    left: 50%;
    width: 120px;
    height: 95px;
    transform: translate(-50%, 0);
  }

  .g-username {
    margin-bottom: 10px;

    img {
      display: none;
      width: 120px;
      height: 113px;
    }
  }

  .g-username:focus-within ~ img {
    display: none;
  }

  .g-username:focus-within {
    input {
      border-color: #007fff;
    }

    img {
      display: block;
    }
  }

  .g-password {
    margin-bottom: 10px;

    img {
      display: none;
      width: 103px;
      height: 84px;
      top: -15%;
    }
  }

  .g-password:focus-within ~ img {
    display: none;
  }

  .g-password:focus-within {
    input {
      border-color: #007fff;
    }

    img {
      display: block;
    }
  }

  ul {
    width: 70vmin;
    height: 70vmin;
    position: relative;
  }

  li {
    /*
      |a|d|a|
      -------  -
      |/   \|  | 0.5h
      |\   /|  ^
      -------
      |  w  |

      a = 0.5h/tan60 = 0.28867513459h
      d = 0.5h/sin60 = 0.57735026919h
      w = a + d + a  = 1.15470053838h

      [zoom out]

          |a|_d_|         _____    _
          /|     \       /     \   |
    ang=60_|      \__d__/       \  | h
         \        /     \       /  |
          \_____|/|      \_____/  _|
                a
         |--------|
             w
    */
    --h: calc(100% / 3);
    --w: calc(1.15470053838 * var(--h));
    --a: calc(0.28867513459 * var(--h));
    --d: calc(0.57735026919 * var(--h));
    height: var(--h);
    width: var(--w);
    position: absolute;
  }

  .left-img {
    display: block;
    width: 100%;
    height: 100%;
    object-fit: cover;
    /*

     [clip-path coordinates]

      --A-F--
     B|/   \|E    coord.
      |\   /|       A(a/w, 0)
      --C-D--       B(0, 50%)
                    C(a/w, 100%)
                    D(100% - a/w, 100%)
                    E(100%, 50%)
                    F(100% - a/w, 0)
                  where a/w = 0.24999999999
    */
    --aw: 24.99999999999%;
    clip-path: polygon(
        var(--aw) 0,
        0 50%,
        var(--aw) 100%,
        calc(100% - var(--aw)) 100%,
        100% 50%,
        calc(100% - var(--aw)) 0
    );
  }

  li:nth-of-type(1) {
    left: calc(50% - var(--a) - var(--d) * 0.5);
    top: 0;
  }

  li:nth-of-type(2) {
    left: calc(50% - var(--a) * 2 - var(--d) * 1.5);
    top: calc(var(--h) * 0.5);
  }

  li:nth-of-type(3) {
    left: calc(50% - var(--a) * 2 - var(--d) * 1.5);
    top: calc(var(--h) * 1.5);
  }

  li:nth-of-type(4) {
    left: calc(50% - var(--a) - var(--d) * 0.5);
    top: calc(var(--h) * 2);
  }

  li:nth-of-type(5) {
    left: calc(50% + var(--d) * 0.5);
    top: calc(var(--h) * 0.5);
  }

  li:nth-of-type(6) {
    left: calc(50% + var(--d) * 0.5);
    top: calc(var(--h) * 1.5);
  }


  /*
  style
  */

  ul {
    filter: drop-shadow(0 0 10px hsla(0, 0%, 0%, 0.3));
  }

  li:hover {
    filter: drop-shadow(0 0 10px powderblue);
    z-index: 10;
  }

  /*
  anim
  */

  ul {
    animation: r 4s forwards;
  }

  @keyframes r {
    from {
      transform: rotate(180deg)
    }
    to {
      transform: rotate(0)
    }
  }

  li {
    animation: ir 4s forwards;
  }

  @keyframes ir {
    from {
      transform: rotate(-360deg)
    }
    to {
      transform: rotate(0)
    }
  }
</style>
