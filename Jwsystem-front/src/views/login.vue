<template>
  <div class="login">
    <el-form ref="loginForm" :model="loginForm" :rules="loginRules" label-position="left" label-width="0px"
             class="login-form">
      <h3 class="title">
        教务管理系统
      </h3>
      <el-form-item prop="username">
        <el-input v-model="loginForm.username" type="text" auto-complete="off" placeholder="账号">
          <svg-icon slot="prefix" icon-class="user" class="el-input__icon input-icon"/>
        </el-input>
      </el-form-item>
      <el-form-item prop="password">
        <el-input v-model="loginForm.password" type="password" auto-complete="off" placeholder="密码"
                  @keyup.enter.native="handleLogin">
          <svg-icon slot="prefix" icon-class="password" class="el-input__icon input-icon"/>
        </el-input>
      </el-form-item>
      <el-form-item prop="code">
        <el-input v-model="loginForm.code" auto-complete="off" placeholder="验证码" style="width: 63%"
                  @keyup.enter.native="handleLogin">
          <svg-icon slot="prefix" icon-class="validCode" class="el-input__icon input-icon"/>
        </el-input>
        <div class="login-code">
          <img :src="codeUrl" @click="getCode">
        </div>
      </el-form-item>
      <el-row style="margin-bottom: 10px">
        <el-col :span="6">
          <el-radio v-model="loginForm.RadioButtonList1" label="管理员">管理员</el-radio>
        </el-col>
        <el-col :span="6">
          <el-radio v-model="loginForm.RadioButtonList1" label="教务人员">教务人员</el-radio>
        </el-col>
        <el-col :span="6" :push="2">
          <el-radio v-model="loginForm.RadioButtonList1" label="教师">教师</el-radio>
        </el-col>
        <el-col :span="6">
          <el-radio v-model="loginForm.RadioButtonList1" label="学生">学生</el-radio>
        </el-col>
      </el-row>
      <el-form-item style="width:100%;">
        <el-button :loading="loading" size="medium" type="primary" style="width:100%;"
                   @click.native.prevent="handleLogin">
          <span v-if="!loading">登 录</span>
          <span v-else>登 录 中...</span>
        </el-button>
      </el-form-item>
    </el-form>
    <!--  底部  -->
    <div v-if="$store.state.settings.showFooter" id="el-login-footer">
      <span v-html="$store.state.settings.footerTxt"/>
      <span> ⋅ </span>
      <a href="http://www.beian.miit.gov.cn" target="_blank">{{ $store.state.settings.caseNumber }}</a>
    </div>
  </div>
</template>

<script>
  import Cookies from 'js-cookie'
  import service from '../utils/request'

  export default {
    name: 'Login',
    data() {
      return {
        codeUrl: 'http://localhost:8080/kaptcha/create',
        cookiePass: '',
        loginForm: {
          username: '1',
          password: '123456',
          code: '',
          RadioButtonList1: '教务人员'
        },
        loginRules: {
          username: [{ required: true, trigger: 'blur', message: '用户名不能为空' }],
          password: [{ required: true, trigger: 'blur', message: '密码不能为空' }],
          code: [{ required: true, trigger: 'change', message: '验证码不能为空' }]
        },
        loading: false,
        redirect: undefined
      }
    },
    watch: {
      $route: {
        handler: function(route) {
          this.redirect = route.query && route.query.redirect
        },
        immediate: true
      }
    },
    methods: {
      getCode() {
        this.codeUrl = 'http://localhost:8080/kaptcha/create?timestamp=' + new Date().getTime()
      },
      handleLogin() {
        this.$refs.loginForm.validate(valid => {
          const user = {
            username: this.loginForm.username,
            password: this.loginForm.password,
            checkcode: this.loginForm.code,
            RadioButtonList1: this.loginForm.RadioButtonList1
          }
          if (valid) {
            this.loading = true
            this.$store.dispatch('Login', user).then(() => {
              this.loading = false
              this.$router.push({ path: '/' })
            }).catch(() => {
              this.codeUrl = 'http://localhost:8080/kaptcha/create?timestamp=' + new Date().getTime()
              this.loading = false
            })
          } else {
            this.codeUrl = 'http://localhost:8080/kaptcha/create?timestamp=' + new Date().getTime()
            console.log('error submit!!')
            return false
          }
        })
      }
    }
  }
</script>

<style rel="stylesheet/scss" lang="scss">
  .login {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100%;
    background-image: url('../assets/images/login_bg.png');
    background-size: cover;
  }

  .title {
    margin: 0 auto 30px auto;
    text-align: center;
    color: #707070;
  }

  .login-form {
    border-radius: 6px;
    background: #ffffff;
    width: 385px;
    padding: 25px 25px 5px 25px;

    .el-input {
      height: 38px;

      input {
        height: 38px;
      }
    }

    .input-icon {
      height: 39px;
      width: 14px;
      margin-left: 2px;
    }
  }

  .login-tip {
    font-size: 13px;
    text-align: center;
    color: #bfbfbf;
  }

  .login-code {
    width: 33%;
    display: inline-block;
    height: 38px;
    float: right;

    img {
      cursor: pointer;
      vertical-align: middle
    }
  }
</style>
