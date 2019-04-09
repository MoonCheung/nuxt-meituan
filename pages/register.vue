<template>
  <div class="page-register">
    <article class="header">
      <header>
        <nuxt-link to="/"
                   class="site-logo" />
        <span class="login">
          <em class="bold">已有美团账号？</em>
          <nuxt-link to="/login">
            <el-button type="primary"
                       size="small">登录</el-button>
          </nuxt-link>
        </span>
      </header>
    </article>
    <section>
      <el-form :model="ruleForm"
               :rules="rules"
               ref="ruleForm"
               label-width="100px"
               class="demo-ruleForm">
        <el-form-item label="昵称"
                      prop="name">
          <el-input v-model="ruleForm.name" />
        </el-form-item>
        <el-form-item label="邮箱"
                      prop="email">
          <el-input v-model="ruleForm.email" />
          <el-button size="mini"
                     round
                     plain
                     @click="sendMsg">发送验证码</el-button>
          <span class="status">{{ statusMsg }}</span>
        </el-form-item>
        <el-form-item label="验证码"
                      prop="code">
          <el-input maxlength="4"
                    v-model="ruleForm.code" />
        </el-form-item>
        <el-form-item label="密码"
                      prop="pwd">
          <el-input type="password"
                    v-model="ruleForm.pwd" />
        </el-form-item>
        <el-form-item label="确认密码"
                      prop="cpwd">
          <el-input type="password"
                    v-model="ruleForm.cpwd" />
        </el-form-item>
        <el-form-item>
          <el-button type="primary"
                     @click="register">同意以下协议并注册</el-button>
          <div class="error">{{ error }}</div>
        </el-form-item>
        <el-form-item>
          <a class="f1"
             href="http://www.meituan.com/about/terms"
             target="_blank">《美团网用户协议》</a>
        </el-form-item>
      </el-form>
    </section>
  </div>
</template>

<script>
export default {
  layout: 'blank',
  data() {
    return {
      statusMsg: '',
      error: '',
      ruleForm: {
        name: '',
        email: '',
        code: '',
        pwd: '',
        cpwd: ''
      },
      rules: {
        name: [
          {
            required: true,
            type: 'string',
            message: '请输入昵称',
            trigger: 'blur'
          }
        ],
        email: [
          {
            required: true,
            type: 'email',
            message: '请输入邮箱',
            trigger: 'blur'
          }
        ],
        pwd: [
          {
            required: true,
            message: '请创建密码',
            trigger: 'blur'
          }
        ],
        cpwd: [
          {
            required: true,
            message: '请再次确认密码',
            trigger: 'blur'
          },
          {
            validator: (rule, value, callback) => {
              if (value === '') {
                callback(new Error('请您再次输入密码'))
              } else if (value !== this.ruleForm.pwd) {
                callback(new Error('两次输入密码不一致'))
              } else {
                callback()
              }
            },
            trigger: 'blur'
          }
        ]
      }
    }
  },
  // 可将方法被混入vue实例
  methods: {
    sendMsg: function() {
      const self = this
      let namePass
      let emailPass
      if (self.timerid) {
        return false
      }
      this.$refs['ruleForm'].validateField('name', valid => {
        namePass = valid
      })
      self.statusMsg = ''
      if (namePass) {
        return false
      }
      this.$refs['ruleForm'].validateField('email', valid => {
        emailPass = valid
      })
      if (!namePass && !emailPass) {
        self.$axios
          .post('/users/verify', {
            username: encodeURIComponent(self.ruleForm.name),
            email: self.ruleForm.email
          })
          .then(({ status, data }) => {
            if (status === 200 && data && data.code === 0) {
              let count = 60
              self.statusMsg = `验证码已发送，剩余${count--}秒`
              self.timerid = setInterval(function() {
                self.statusMsg = `验证码已发送，剩余${count--}秒`
                if (count === 0) {
                  clearInterval(self.timerid)
                }
              }, 1000)
            } else {
              self.statusMsg = data.msg
            }
          })
      }
    },
    register: function() {}
  }
}
</script>

<style lang="scss">
  @import '@/assets/css/register/index.scss';
</style>

