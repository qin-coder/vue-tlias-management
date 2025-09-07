<script setup>
import { ref } from "vue";
const searchEmp = ref({
  name: "",
  gender: "",
  date: "",
});

//清空
const clear = () => {
  searchEmp.value = { name: "", gender: "", date: [], begin: "", end: "" };
  search();
};
</script>

<template>
  <h1>Employee management</h1>

  <!-- 搜索栏 -->
  <div class="container">
    <el-form :inline="true" :model="searchEmp" class="demo-form-inline">
      <el-form-item label="name">
        <el-input
          v-model="searchEmp.name"
          placeholder="Please enter the employee's name"
        />
      </el-form-item>

      <el-form-item label="gender">
        <el-select
          v-model="searchEmp.gender"
          placeholder="Please select gender"
        >
          <el-option label="male" value="1" />
          <el-option label="female" value="2" />
        </el-select>
      </el-form-item>

      <el-form-item label="Date of entry">
        <el-date-picker
          v-model="searchEmp.date"
          type="daterange"
          range-separator="to"
          start-placeholder="start date"
          end-placeholder="end date"
          value-format="YYYY-MM-DD"
        />
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="search">Select</el-button>
        <el-button type="info" @click="clear">Clear</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<style scoped>
.container {
  margin: 10px 0px;
}

.avatar {
  height: 40px;
}
.avatar-uploader .avatar {
  width: 78px;
  height: 78px;
  display: block;
}
.avatar-uploader .el-upload {
  border: 1px dashed var(--el-border-color);
  border-radius: 6px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
  transition: var(--el-transition-duration-fast);
}

.avatar-uploader .el-upload:hover {
  border-color: var(--el-color-primary);
}

.el-icon.avatar-uploader-icon {
  font-size: 28px;
  color: #8c939d;
  width: 78px;
  height: 78px;
  text-align: center;
  border-radius: 10px;
  /* 添加灰色的虚线边框 */
  border: 1px dashed var(--el-border-color);
}
</style>
