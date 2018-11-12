<template>
  <div class="home">
    <el-container class="home">
      <el-header>
        <div class="logo"></div>
        <div class="logout">
          欢迎光临
          <a href="#" @click.prevent='quitFn'>退出
            <el-button type="text" >您确定要退出吗</el-button>
          </a>
        </div>
        <h1>电商管理后台系统</h1>
      </el-header>
      <el-container>
        <el-aside width="200px">
          <el-menu
                default-active="/Users"
                class="el-menu-vertical-demo"
                background-color="#545c64"
                text-color="#fff"
                active-text-color="#ffd04b"
                unique-opened
                router>
            <el-submenu index="1">
              <template slot="title">
                <i class="el-icon-location"></i>
                <span>用户管理</span>
              </template>
              <el-menu-item index="/Users">用户列表</el-menu-item>
            </el-submenu>
            <el-submenu index="2">
              <template slot="title">
                <i class="el-icon-location"></i>
                <span>权限管理</span>
              </template>
              <el-menu-item index="2-1">角色列表</el-menu-item>
              <el-menu-item index="2-1">权限列表</el-menu-item>
            </el-submenu>
            <el-submenu index="3">
              <template slot="title">
                <i class="el-icon-location"></i>
                <span>商品管理</span>
              </template>
              <el-menu-item index="3-1">商品列表</el-menu-item>
              <el-menu-item index="3-1">分类参数</el-menu-item>
              <el-menu-item index="3-1">商品分类</el-menu-item>
            </el-submenu>
            <el-submenu index="4">
              <template slot="title">
                <i class="el-icon-location"></i>
                <span>订单管理</span>
              </template>
              <el-menu-item index="4-1">订单列表</el-menu-item>
            </el-submenu>
            <el-submenu index="5">
              <template slot="title">
                <i class="el-icon-location"></i>
                <span>数据统计</span>
              </template>
              <el-menu-item index="5-1">数据报表</el-menu-item>
            </el-submenu>
          </el-menu>
        </el-aside>
        <el-main>
          <router-view></router-view>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<script>
export default {
  methods: {
    quitFn() {
      this.$confirm('您确定退出吗?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(() => {
          this.$message({
            type: 'success',
            message: '退出成功!',
            duration: 1000
          })
          localStorage.removeItem('token')
          this.$router.push('/login')
        })
        .catch(() => {
          this.$message({
            type: 'info',
            message: '已取消退出',
            duration: 1000
          })
        })
    }
  }
}
</script>

<style scoped lang='less'>
.home {
  height: 100%;
}
.el-header {
  background: #b3c1cd;
  height: 60px;
  .logo {
    float: left;
    width: 180px;
    height: 60px;
    background-image: url('../assets/logo.png');
    background-size: contain;
    background-position: center;
    background-repeat: no-repeat;
  }
  .logout {
    float: right;
    width: 180px;
    height: 60px;
    line-height: 60px;
    text-align: center;
    font-weight: bold;
    a {
      color: darkorange;
    }
  }
  h1 {
    overflow: hidden;
    text-align: center;
    line-height: 60px;
    font-size: 28px;
    color: #ffffff;
    font-weight: 700;
  }
}
.el-aside {
  width: 200px;
  background-color: #545c64;
  .el-submenu {
    width: 200px;
  }
}
.el-main {
  background-color: #d4dfe4;
}
</style>
