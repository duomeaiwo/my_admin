<template>
<div class="login">
  <el-form ref="form" :model="form" label-width="80px" :rules="rules" status-icon>
    <img src="../assets/2.jpg" alt="">
    <el-form-item label="用户名" prop="username">
      <el-input v-model="form.username" placeholder="请输入用户名"></el-input>
    </el-form-item>
    <el-form-item label="密码" prop="password">
      <el-input v-model="form.password" placeholder="请输入密码" type="password" @keyup.enter.native="login"></el-input>
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click='login'>登录</el-button>
      <el-button @click='reset'>重置</el-button>
    </el-form-item>
  </el-form>
</div>
</template>

<script>
import axios from 'axios'
export default {
  data() {
    return {
      form: {
        username: '',
        password: ''
      },
      rules: {
        username: [
          { required: true, message: '用户名不能为空', trigger: 'change' },
          { min: 3, max: 6, message: '长度在 3 到 6 个字符', trigger: 'change' }
        ],
        password: [
          { required: true, message: '密码不能为空', trigger: 'change' },
          {
            min: 6,
            max: 12,
            message: '长度在 6 到 12 个字符',
            trigger: 'change'
          }
        ]
      }
    }
  },
  methods: {
    reset() {
      this.$refs.form.resetFields()
    },
    fn() {
      alert(1)
    },
    login() {
      this.$refs.form.validate(valid => {
        if (valid) {
          axios({
            url: 'http://localhost:8888/api/private/v1/login',
            method: 'post',
            data: this.form
          }).then(res => {
            if (res.data.meta.status === 200) {
              this.$message.success('登陆成功')
              localStorage.setItem('token', res.data.data.token)
              this.$router.push('/Home')
            } else {
              this.$message({
                type: 'error',
                message: res.data.meta.msg
              })
            }
          })
        } else {
          console.log('error submit!!')
          return false
        }
      })
    }
  }
}
</script>

<style lang='less' scoped>
.login {
  height: 100%;
  background-color: #2d434c;
  overflow: hidden;
  .el-form {
    margin: 200px auto;
    width: 400px;
    background: #fff;
    padding: 75px 40px 15px;
    border-radius: 20px;
    position: relative;
  }
  img {
    width: 130px;
    height: 130px;
    border-radius: 50%;
    position: absolute;
    top: -90px;
    left: 50%;
    transform: translateX(-50%);
    border: 10px solid #fff;
  }
}
</style>
