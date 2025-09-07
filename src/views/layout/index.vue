<script setup>
import { RouterView } from "vue-router";
</script>

<template>
  <div class="common-layout">
    <el-container>
      <!-- Header 区域 -->
      <el-header class="header">
        <span class="title">Tlias Smart Learning Assistance System</span>
        <span class="right_tool">
          <a href="">
            <el-icon><EditPen /></el-icon> Change password &nbsp;&nbsp;&nbsp; |
            &nbsp;&nbsp;&nbsp;
          </a>
          <a href="">
            <el-icon><SwitchButton /></el-icon> Sign Out
          </a>
        </span>
      </el-header>

      <el-container>
        <!-- 左侧菜单 -->
        <el-aside width="600px" class="aside">
          <el-menu router>
            <!-- 首页菜单 -->
            <el-menu-item index="/index">
              <el-icon><Promotion /></el-icon> Home
            </el-menu-item>
            <el-sub-menu index="manage">
              <template #title>
                <el-icon><HomeFilled /></el-icon>Class&Student Management
              </template>

              <el-menu-item index="clazz"
                ><el-icon><Bell /></el-icon>Class Management</el-menu-item
              >
              <el-menu-item index="stu"
                ><el-icon><User /></el-icon>Student Management</el-menu-item
              >
            </el-sub-menu>
            <!-- 系统信息管理 -->
            <el-sub-menu index="/system">
              <template #title>
                <el-icon><Tools /></el-icon>System Information Management
              </template>
              <el-menu-item index="/dept">
                <el-icon><HelpFilled /></el-icon>Department Management
              </el-menu-item>
              <el-menu-item index="/emp">
                <el-icon><Avatar /></el-icon>Employee Management
              </el-menu-item>
            </el-sub-menu>

            <!-- 数据统计管理 -->
            <el-sub-menu index="/report">
              <template #title>
                <el-icon><Histogram /></el-icon>Data Statistics Management
              </template>
              <el-menu-item index="/empReport">
                <el-icon><InfoFilled /></el-icon>Employee Information Statistics
              </el-menu-item>
              <el-menu-item index="/stuReport">
                <el-icon><Share /></el-icon>Student Information Statistics
              </el-menu-item>
              <el-menu-item index="/log">
                <el-icon><Document /></el-icon>Log Information Statistics
              </el-menu-item>
            </el-sub-menu>
          </el-menu>
        </el-aside>

        <el-main>
          <router-view></router-view>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<style scoped>
.header {
  background-image: linear-gradient(
    to right,
    #00547d,
    #007fa4,
    #00aaa0,
    #00d072,
    #a8eb12
  );
}

.title {
  color: white;
  font-size: 40px;
  font-family: 楷体;
  line-height: 60px;
  font-weight: bolder;
}

.right_tool {
  float: right;
  line-height: 60px;
}

a {
  color: white;
  text-decoration: none;
}

.aside {
  width: 310px;
  border-right: 1px solid #ccc;
  height: 730px;
}
</style>
