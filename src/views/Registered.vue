<template>
  <div>
    <div style="margin-left: 15%;margin-right: 15%;margin-top: 1%;">
      <div style="">
        <p style="font-size: 35px;color: #f84e4e;width: 100%;text-align: center">small<span
          style="font-size: 30px">小商品</span></p>
      </div>
      <div style="width: 40%;padding-left: 30%;padding-right: 30%">
        <el-form
          :model="myForm"
          :rules="myRule"
          status-icon
          ref="myForm"
          label-position="left"
          label-width="0px"
          class="demo-ruleForm login-page"
        >
          <el-form-item prop="tel">
            <el-input
              type="contact"
              v-model="myForm.tel"
              auto-complete="off"
              placeholder="请输入手机号"
            />
          </el-form-item>

          <el-form-item prop="name">
            <el-input type="text" v-model="myForm.name" placeholder="请输入昵称"/>
          </el-form-item>

          <el-form-item prop="firstPassword">
            <el-input
              type="password"
              v-model="myForm.firstPassword"
              auto-complete="off"
              placeholder="请输入密码"
            />
          </el-form-item>

          <el-form-item prop="secondPassword">
            <el-input
              type="password"
              v-model="myForm.secondPassword"
              auto-complete="off"
              placeholder="请再次输入密码"
            />
          </el-form-item>


          <el-form-item style="width:100%;">
            <el-button
              type="danger"
              style="width:100%;"
              @click="handleSubmit"
              :loading="loading"
            >注册
            </el-button>
          </el-form-item>
          <el-form-item style="width:100%;">
            <el-button type="text" @click="toLogin" style="margin-left: auto;color: #F56C6C">已有账号?前往登录</el-button>
          </el-form-item>
        </el-form>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'Registered',
    data () {
      const validatePass1 = (rule, value, callback) => {
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
      const validatePass2 = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请再次输入密码'))
        } else if (value.length < 6) {
          callback(new Error('密码长度最小6位'))
        } else if (value.length > 12) {
          callback(new Error('密码长度最大12位'))
        } else if (value !== this.myForm.firstPassword) {
          callback(new Error('两次输入密码不一致!'))
        } else {
          callback()
        }
      }
      return {
        loading: false,
        myForm: {
          name: '',
          firstPassword: '',
          secondPassword: '',
          tel: ''
        },
        myRule: {
          account: [{
            required: true,
            message: '请输入登录账号',
            trigger: 'blur'
          }],
          name: [{
            required: true,
            message: '请输入昵称',
            trigger: 'blur'
          }],
          firstPassword: [{
            validator: validatePass1,
            trigger: 'blur'
          }],
          secondPassword: [{
            validator: validatePass2,
            trigger: 'blur'
          }],
          select: [{
            required: true,
            message: '请选择用户类型',
            trigger: 'blur'
          }],
          tel: [{
            required: true,
            message: '请输入手机号码',
            trigger: 'blur'
          },
            {
              validator: function (rule, value, callback) {
                if (/^1[34578]\d{9}$/.test(value) === false) {
                  callback(new Error('请输入正确的手机号'))
                } else {
                  callback()
                }
              },
              trigger: 'blur'
            }],
        },
      }
    },
    methods: {
      handleSubmit () {
        let params = new URLSearchParams()
        params.append('userName', this.myForm.name)
        params.append('tel', this.myForm.tel)
        params.append('password', this.myForm.secondPassword)
        this.$axios({
          method: 'post',
          url: '/api/customer/registered',
          data: params
        }).then(res => {
          if (res.data.status === 'success') {
            this.$message.success('注册成功，3秒后自动跳转到登录页面')
            setTimeout(() => {
              this.$router.push('/login')
            }, 3000)
          } else {
            this.$message.error(res.data.data.errMsg)
          }
        }).catch(err => {
          this.$message.error('Unknown error')
        })
      },
      toLogin () {
        this.$router.push('/login')
      }
    }
  }
</script>

<style scoped>

</style>
