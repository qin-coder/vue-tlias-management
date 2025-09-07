<script setup>
import { ref, onMounted } from "vue";
import { queryAllApi } from "@/api/dept";

//钩子函数
onMounted(() => {
  search();
})

//查询
const deptList = ref([])
const search = async () => {
  const result = await queryAllApi();
  if(result.code){
    deptList.value = result.data;
  }
}


</script>

<template>
  <h1>Department Management</h1>
  <div class="container">
    <el-button type="primary" @click="addDept"> + Add Department</el-button>
  </div>

  <div class="container">
    <el-table :data="deptList" border style="width: 100%">
      <el-table-column type="index" label="No" width="100" align="center" />
      <el-table-column
        prop="name"
        label="Department Name"
        width="300"
        align="center"
      />
      <el-table-column
        prop="updateTime"
        label="last update time"
        width="350"
        align="center"
      />
      <el-table-column label="Operation" align="center">
        <template #default="scope">
          <el-button type="primary" size="small" @click="edit(scope.row.id)"
            ><el-icon><EditPen /></el-icon> Edit</el-button
          >
          <el-button type="danger" size="small" @click="delById(scope.row.id)"
            ><el-icon><Delete /></el-icon> Delete</el-button
          >
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<style scoped>
.container {
  margin: 10px 0px;
}
</style>
