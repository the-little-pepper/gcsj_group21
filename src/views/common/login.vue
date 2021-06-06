<template>
  <div class="site-wrapper site-page--login">
    <div class="site-content__wrapper">
      <div class="site-content">
        <div class="brand-info">
          <h2 class="brand-info__text">到云</h2>
          <p class="brand-info__intro">工程实践第21小组。</p>
        </div>
        <div class="login-main">
          <h3 class="login-title">管理员登录</h3>
          <el-form :model="dataForm" :rules="!loginmethod ? dataRule : coderule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" status-icon>
            <el-form-item prop="userName">
              <el-input v-if="!loginmethod" v-model="dataForm.userName" placeholder="手机号或账号" style="width: 100%"></el-input>
              <el-input v-if="loginmethod" v-model="dataForm.userName" placeholder="手机号" style="width: 65%"></el-input>
              <el-button v-if="loginmethod" @click="sendCode" :disabled="!show"><span v-if="show">发送验证码</span><span v-if="!show">剩余 {{count}} (s)</span></el-button>
            </el-form-item>

            <el-form-item v-if="loginmethod" prop="code">
              <el-input
                ref="code"
                v-model="dataForm.code"
                placeholder="短信验证码"
                name="code"
                type="text"
                tabindex="1"
              />
            </el-form-item>

            <el-form-item v-if="!loginmethod" prop="password">
              <el-input v-model="dataForm.password" type="password" placeholder="密码"></el-input>
            </el-form-item>
            <el-form-item prop="captcha" v-if="!loginmethod">
              <el-row :gutter="20">
                <el-col :span="14">
                  <el-input v-model="dataForm.captcha" placeholder="验证码">
                  </el-input>
                </el-col>
                <el-col :span="10" class="login-captcha">
                  <img :src="captchaPath" @click="getCaptcha()" alt="">
                </el-col>
              </el-row>
            </el-form-item>

          <div class="btnup">
            <div class="autologin">
              <!-- <input id="" type="checkbox" name="" />
              <span>自动登录</span> -->
            </div>
            <div class="forget">
              <!-- <span @click="handleReg">前往注册</span> -->
              <!-- <span>忘记密码</span> -->
              <span @click="alterLoginMethod">{{
                !loginmethod ? "验证码登录" : "密码登录"
              }}</span>
            </div>
          </div>

            <el-form-item>
              <el-button class="login-btn-submit" type="primary" @click="dataFormSubmit()">登录</el-button>
            </el-form-item>
          </el-form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import { getUUID } from '@/utils'
  import { isMobile } from '@/utils/validate'
  export default {
    data () {
      const validateUsername = (rule, value, callback) => {
        if (!isMobile(value)) {
          callback(new Error('请输入正确的账号，账号为手机号'))
        } else {
          callback()
        }
      }

      return {
        loginmethod: false,
        coderule: {
          userName: [
            { required: true, validator: validateUsername, trigger: 'blur' }
          ],
          code: [{ required: true, message: '短信验证码不能为空', trigger: 'blur' }]
        },
        loading: false,
        passwordType: 'password',
        redirect: undefined,

        timer: null,
        count: '',
        show: true,
        dataForm: {
          userName: '',
          password: '',
          uuid: '',
          captcha: '',
          code: ''
        },
        dataRule: {
          userName: [
            { required: true, message: '账号不能为空', trigger: 'blur' }
          ],
          password: [
            { required: true, message: '密码不能为空', trigger: 'blur' }
          ],
          captcha: [
            { required: true, message: '验证码不能为空', trigger: 'blur' }
          ]
        },
        captchaPath: ''
      }
    },
    created () {
      this.getCaptcha()
    },
    methods: {
      // 提交表单
      dataFormSubmit () {
        if (!this.loginmethod) {
          this.$refs['dataForm'].validate((valid) => {
            if (valid) {
              this.$http({
                url: this.$http.adornUrl('/sys/login'),
                method: 'post',
                data: this.$http.adornData({
                  'username': this.dataForm.userName,
                  'password': this.dataForm.password,
                  'uuid': this.dataForm.uuid,
                  'captcha': this.dataForm.captcha
                })
              }).then(({data}) => {
                if (data && data.code === 200) {
                  this.$cookie.set('token', data.token)
                  this.$router.replace({ name: 'home' })
                } else {
                  this.getCaptcha()
                  this.$message.error(data.msg)
                }
              })
            }
          })
        } else {
          this.$refs['dataForm'].validate((valid) => {
            if (valid) {
              this.$http({
                url: this.$http.adornUrl('/sys/codeLogin'),
                method: 'post',
                data: this.$http.adornData({
                  'account': this.dataForm.userName,
                  'verificationCode': this.dataForm.code,
                  'uuid': this.dataForm.uuid
                })
              }).then(({data}) => {
                if (data && data.code === 200) {
                  this.$cookie.set('token', data.token)
                  this.$router.replace({ name: 'home' })
                } else {
                  this.getCaptcha()
                  this.$message.error(data.msg)
                }
              })
            }
          })
        }
      },
      // 获取验证码
      getCaptcha () {
        this.dataForm.uuid = getUUID()
        this.captchaPath = this.$http.adornUrl(`/captcha.jpg?uuid=${this.dataForm.uuid}`)
      },
      handleReg () {
        this.$router.push({ path: '/register' })
      },
      alterLoginMethod () {
        this.loginmethod = !this.loginmethod
        this.getCaptcha()
      },
      // 获取手机验证码
      sendCode () {
        this.$http({
          url: this.$http.adornUrl('/app/user/checkCode'),
          method: 'post',
          data: this.$http.adornData({
            'phonenumber': this.dataForm.userName
          })
        }).then(({data}) => {
          if (data && data.code === 200) {
            this.$message({
              message: '发送成功',
              type: 'success'
            })
            const TIME_COUNT = 60
            if (!this.timer) {
              this.count = TIME_COUNT
              this.show = false
              this.timer = setInterval(() => {
                if (this.count > 0 && this.count <= TIME_COUNT) {
                  this.count--
                } else {
                  this.show = true
                  clearInterval(this.timer)
                  this.timer = null
                }
              }, 1000)
            }
          } else {
            this.$message.error(data.msg)
          }
        })
      }
    }
  }
</script>

<style lang="scss">
  .site-wrapper.site-page--login {
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    background-color: rgba(38, 50, 56, .6);
    overflow: hidden;
    &:before {
      position: fixed;
      top: 0;
      left: 0;
      z-index: -1;
      width: 100%;
      height: 100%;
      content: "";
      background-image: url(~@/assets/img/login_bg.jpg);
      background-size: cover;
    }
    .site-content__wrapper {
      position: absolute;
      top: 0;
      right: 0;
      bottom: 0;
      left: 0;
      padding: 0;
      margin: 0;
      overflow-x: hidden;
      overflow-y: auto;
      background-color: transparent;
    }
    .site-content {
      min-height: 100%;
      padding: 30px 500px 30px 30px;
    }
    .brand-info {
      margin: 220px 100px 0 90px;
      color: #fff;
    }
    .brand-info__text {
      margin:  0 0 22px 0;
      font-size: 48px;
      font-weight: 400;
      text-transform : uppercase;
    }
    .brand-info__intro {
      margin: 10px 0;
      font-size: 16px;
      line-height: 1.58;
      opacity: .6;
    }
    .login-main {
      position: absolute;
      top: 0;
      right: 0;
      padding: 150px 60px 180px;
      width: 470px;
      min-height: 100%;
      background-color: #fff;
    }
    .login-title {
      font-size: 16px;
    }
    .login-captcha {
      overflow: hidden;
      > img {
        width: 100%;
        cursor: pointer;
      }
    }
    .login-btn-submit {
      width: 100%;
      margin-top: 38px;
    }

    .btnup {
    width: 520px;
    max-width: 100%;
    height: 40px;
    margin-bottom: 20px;
    display: flex;
    justify-content: space-between;

    .autologin {
      width: 250px;
      height: 40px;
      line-height: 40px;
      font-size: 14px;
      input {
        margin-right: 10px;
      }
    }
    .forget {
      width: 300px;
      height: 40px;
      line-height: 40px;
      font-size: 14px;
      span {
        float: right;
        color: rgb(78, 173, 211);
        margin-left: 15px;
        cursor: pointer;
      }
    }
  }

  }
</style>
