<template>
  <div class="user">
    <!-- 面包屑导航 -->
    <el-breadcrumb separator-class="el-icon-arrow-right">
      <el-breadcrumb-item :to="{ path: '/home' }">用户管理</el-breadcrumb-item>
      <el-breadcrumb-item>用户列表</el-breadcrumb-item>
    </el-breadcrumb>
    <!-- 搜索框功能 -->
    <div style="margin-bottom: 5px;">
      <el-input placeholder="请输入关键字" v-model="query" class="input-with-select" >
        <el-button slot="append" icon="el-icon-search" @click='search'></el-button>
      </el-input>
      <el-button plain type='success' @click='showadd'>用户添加</el-button>
    </div>
    <!-- 表格 -->
    <el-table
          :data="userlist"
          style="width: 100%">
      <el-table-column
        prop="username"
        label="姓名"
        width="180">
      </el-table-column>
      <el-table-column
        prop="email"
        label="邮箱"
        width="180">
      </el-table-column>
      <el-table-column
        prop="mobile"
        label="电话"
        width="180">
      </el-table-column>
      <el-table-column
        prop="mg_state"
        label="用户状态"
        width="180">
        <template slot-scope="scope">
          <el-switch
            v-model="scope.row.mg_state"
            active-color="#13ce66"
            inactive-color="#ff4949"
            @change='changestate(scope.row)'
            >
          </el-switch>
        </template>
      </el-table-column>
      <el-table-column
        label="操作">
        <template slot-scope="scope">
           <el-button plain size="small" type="primary" icon="el-icon-edit" @click='showEdit(scope.row)'></el-button>
           <el-button plain size="small" type="danger" icon="el-icon-delete" @click='del(scope.row.id)' ></el-button>
           <el-button plain size="small" type="success" icon="el-icon-check">分配角色</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页 -->
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      background
      :total="total"
      :page-size="pageSize"
      :current-page="currentPage"
      :page-sizes="[2, 3, 4, 6]"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <!-- 对话框组件 -->
    <el-dialog
      title="添加用户"
      :visible.sync="addvisible"
      width="40%">
      <el-form ref='addform' :model='addform' label-width='80px' :rules='rules'>
        <el-form-item label="用户名" prop='username'>
          <el-input v-model="addform.username" ></el-input>
        </el-form-item>
        <el-form-item label="密码" prop='password'>
          <el-input v-model="addform.password" ></el-input>
        </el-form-item>
        <el-form-item label="邮箱" prop='email'>
          <el-input v-model="addform.email" ></el-input>
        </el-form-item>
        <el-form-item label="电话" prop='mobile'>
          <el-input v-model="addform.mobile" ></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="addvisible = false">取 消</el-button>
        <el-button type="primary" @click="addUser">确 定</el-button>
      </span>
    </el-dialog>
    <!-- edit对话框 -->
    <el-dialog
      title="编辑用户"
      :visible.sync="editvisible"
      width="40%">
      <el-form ref='editform' :model='editform' label-width='80px' :rules='rules'>
        <el-form-item label="用户名">
          <el-tag> {{editform.username}} </el-tag>
        </el-form-item>
        <el-form-item label="邮箱" prop='email'>
          <el-input v-model="editform.email" ></el-input>
        </el-form-item>
        <el-form-item label="手机" prop='mobile'>
          <el-input v-model="editform.mobile" ></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="editvisible = false">取 消</el-button>
        <el-button type="primary" @click="editUser">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  data() {
    return {
      query: '',
      currentPage: 1,
      pageSize: 2,
      total: 0,
      userlist: [],
      addvisible: false,
      editvisible: false,
      addform: {
        username: '',
        password: '',
        mobile: '',
        email: ''
      },
      editform: {
        username: '',
        email: '',
        mobile: '',
        id: ''
      },
      rules: {
        username: [
          { required: true, message: '请输入用户名称', trigger: 'change' },
          { min: 3, max: 6, message: '长度在 3 到 6 个字符', trigger: 'change' }
        ],
        password: [
          { required: true, message: '请输入密码名称', trigger: 'change' },
          {
            min: 6,
            max: 12,
            message: '长度在 6 到 12 个字符',
            trigger: 'change'
          }
        ],
        email: [
          { type: 'email', message: '请输入正确的格式', trigger: 'change' }
        ],
        mobile: [
          {
            pattern: /^1(3|4|5|7|8)\d{9}$/,
            message: '请输入正确的手机号',
            trigger: 'change'
          }
        ]
      }
    }
  },
  methods: {
    getList() {
      axios({
        method: 'get',
        url: 'users',
        params: {
          query: this.query,
          pagenum: this.currentPage,
          pagesize: this.pageSize
        }
      }).then(res => {
        if (res.meta.status === 200) {
          this.userlist = res.data.users
          this.total = res.data.total
        }
      })
    },
    handleSizeChange(val) {
      this.pageSize = val
      this.currentPage = 1
      this.getList()
    },
    handleCurrentChange(val) {
      this.currentPage = val
      this.getList()
    },
    search() {
      this.currentPage = 1
      this.getList()
    },
    del(id) {
      console.log(id)
      this.$confirm('您确定要删除该用户吗?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(() => {
          console.log('删除成功')
          axios({
            method: 'delete',
            url: `users/${id}`
          }).then(res => {
            console.log(res)
            if (res.meta.status === 200) {
              if (this.userlist.length === 1 && this.currentPage > 1) {
                this.currentPage--
              }
              this.getList()
              this.$message.success('删除成功')
            }
          })
        })
        .catch(() => {
          this.$message.info('已取消删除')
        })
    },
    changestate(user) {
      axios({
        url: `users/${user.id}/state/${user.mg_state}`,
        method: 'put'
      }).then(res => {
        if (res.meta.status === 200) {
          this.$message.success('修改成功了')
        } else {
          this.$message.error('修改失败了')
        }
      })
    },
    showadd() {
      this.addvisible = true
    },
    addUser() {
      this.$refs.addform.validate(valid => {
        if (!valid) return false
        this.axios({
          url: 'users',
          method: 'post',
          data: this.addform
        }).then(res => {
          if (res.meta.status === 201) {
            this.total++
            this.currentPage = Math.ceil(this.total / this.pageSize)
            this.getList()
            this.addvisible = false
            this.$refs.addform.resetFields()
          }
        })
      })
    },
    showEdit(user) {
      this.editvisible = true
      this.editform.username = user.username
      this.editform.mobile = user.mobile
      this.editform.email = user.email
      this.editform.id = user.id
    },
    editUser() {
      this.$refs.editform.validate(valid => {
        if (!valid) return false
        this.axios({
          url: `users/${this.editform.id}`,
          method: 'put',
          data: this.editform
        }).then(res => {
          if (res.meta.status === 200) {
            this.getList()
            this.editvisible = false
            this.$refs.editform.resetFields()
          }
        })
      })
    }
  },
  created() {
    this.getList()
  },
  watch: {
    query: function(val) {
      if (val === '') {
        this.getList()
      }
    }
  }
}
</script>

<style scoped lang='less'>
.el-breadcrumb {
  height: 30px;
  line-height: 30px;
}
.el-input {
  width: 400px;
}
</style>
