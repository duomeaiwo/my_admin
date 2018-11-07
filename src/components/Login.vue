<template>
  <el-form :model="dynamicValidateForm" ref="dynamicValidateForm" label-width="100px" class="demo-dynamic">
    <el-form-item
      prop="email"
      label="用户名"
      :rules="[
        { required: true, message: '请输入邮箱地址', trigger: 'blur' },
        { type: 'email', message: '请输入正确的邮箱地址', trigger: ['blur', 'change'] }
      ]"
    >
      <el-input v-model="dynamicValidateForm.email"></el-input>
    </el-form-item>
    <el-form-item
      v-for="(domain, index) in dynamicValidateForm.domains"
      :label="'密码'"
      :key="domain.key"
      :prop="'domains.' + index + '.value'"
      :rules="{
        required: true, message: '域名不能为空', trigger: 'blur'
      }"
    >
      <el-input v-model="domain.value" ></el-input>
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="submitForm('dynamicValidateForm')">登录</el-button>
      <el-button @click="resetForm('dynamicValidateForm')">重置</el-button>
    </el-form-item>
  </el-form>
</template>

<script>
export default {
  data() {
    return {
      dynamicValidateForm: {
        domains: [
          {
            value: ''
          }
        ],
        email: ''
      }
    }
  },
  methods: {
    submitForm(formName) {
      this.$refs[formName].validate(valid => {
        if (valid) {
          alert('submit!')
        } else {
          console.log('error submit!!')
          return false
        }
      })
    },
    resetForm(formName) {
      this.$refs[formName].resetFields()
    },
    removeDomain(item) {
      var index = this.dynamicValidateForm.domains.indexOf(item)
      if (index !== -1) {
        this.dynamicValidateForm.domains.splice(index, 1)
      }
    },
    addDomain() {
      this.dynamicValidateForm.domains.push({
        value: '',
        key: Date.now()
      })
    }
  }
}
</script>

<style lang='less'>
html,
body {
  background-color: #2d434c;
}
.el-form {
  width: 450px;
  height: 300px;
  border-radius: 10px;
  background: #fff;
  margin: 100px auto;
  text-align: center;
  padding: 10px;
}
</style>
