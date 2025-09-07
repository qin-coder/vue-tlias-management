<script setup>
import { ref, watch, onMounted } from "vue";
import { queryPageApi, addApi } from "@/api/emp";
import { queryAllApi as queryAllDeptApi } from "@/api/dept";
import { ElMessage } from "element-plus";

//元数据
//职位列表数据
const jobs = ref([
  { name: "班主任", value: 1 },
  { name: "讲师", value: 2 },
  { name: "学工主管", value: 3 },
  { name: "教研主管", value: 4 },
  { name: "咨询师", value: 5 },
  { name: "其他", value: 6 },
]);
//性别列表数据
const genders = ref([
  { name: "男", value: 1 },
  { name: "女", value: 2 },
]);
//部门列表数据
const depts = ref([]);

//搜索表单对象
const searchEmp = ref({ name: "", gender: "", date: [], begin: "", end: "" });

//侦听searchEmp的date属性
watch(
  () => searchEmp.value.date,
  (newVal, oldVal) => {
    if (newVal.length == 2) {
      searchEmp.value.begin = newVal[0];
      searchEmp.value.end = newVal[1];
    } else {
      searchEmp.value.begin = "";
      searchEmp.value.end = "";
    }
  }
);

//钩子函数
onMounted(() => {
  search(); //查询员工列表数据
  queryAllDepts(); //查询所有部门列表数据
});

//查询所有部门数据
const queryAllDepts = async () => {
  const result = await queryAllDeptApi();
  if (result.code) {
    depts.value = result.data;
  }
};

//查询员工列表
const search = async () => {
  const result = await queryPageApi(
    searchEmp.value.name,
    searchEmp.value.gender,
    searchEmp.value.begin,
    searchEmp.value.end,
    currentPage.value,
    pageSize.value
  );
  if (result.code) {
    empList.value = result.data.rows;
    total.value = result.data.total;
  }
};

//清空
const clear = () => {
  searchEmp.value = { name: "", gender: "", date: [], begin: "", end: "" };
  search();
};

//员工列表数据
const empList = ref([]);

//分页
const currentPage = ref(1); //页码
const pageSize = ref(10); //每页展示记录数
const background = ref(true); //背景色
const total = ref(0); //总记录数

//每页展示记录数变化
const handleSizeChange = (val) => {
  search();
};
//页码变化时触发
const handleCurrentChange = (val) => {
  search();
};

//新增员工
const addEmp = () => {
  dialogVisible.value = true;
  dialogTitle.value = "Add Employee";
  employee.value = {
    username: "",
    name: "",
    gender: "",
    phone: "",
    job: "",
    salary: "",
    deptId: "",
    entryDate: "",
    image: "",
    exprList: [],
  };

  //重置表单的校验规则-提示信息
  if (empFormRef.value) {
    empFormRef.value.resetFields();
  }
};

//新增/修改表单
const employee = ref({
  username: "",
  name: "",
  gender: "",
  phone: "",
  job: "",
  salary: "",
  deptId: "",
  entryDate: "",
  image: "",
  exprList: [],
});

// 控制弹窗
const dialogVisible = ref(false);
const dialogTitle = ref("New Employee");

// 文件上传
// 图片上传成功后触发
const handleAvatarSuccess = (response) => {
  employee.value.image = response.data;
};
// 文件上传之前触发
const beforeAvatarUpload = (rawFile) => {
  if (rawFile.type !== "image/jpeg" && rawFile.type !== "image/png") {
    ElMessage.error("Only supports uploading images");
    return false;
  } else if (rawFile.size / 1024 / 1024 > 10) {
    ElMessage.error("Images up to 10MB only");
    return false;
  }
  return true;
};

//添加工作经历
const addExprItem = () => {
  employee.value.exprList.push({
    company: "",
    job: "",
    begin: "",
    end: "",
    exprDate: [],
  });
};
//删除工作经历
const delExprItem = (index) => {
  employee.value.exprList.splice(index, 1);
};
//侦听-employee员工对象中的工作经历信息
watch(
  () => employee.value.exprList,
  (newVal, oldVal) => {
    if (employee.value.exprList && employee.value.exprList.length > 0) {
      employee.value.exprList.forEach((expr) => {
        expr.begin = expr.exprDate[0];
        expr.end = expr.exprDate[1];
      });
    }
  },
  { deep: true }
); //深度侦听

//保存员工
const save = async () => {
  //表单校验
  if (!empFormRef.value) return;
  empFormRef.value.validate(async (valid) => {
    //valid 表示是否校验通过: true 通过 / false  不通过
    if (valid) {
      //通过
      const result = await addApi(employee.value);
      if (result.code) {
        //成功
        ElMessage.success("Saved successfully");
        dialogVisible.value = false;
        search();
      } else {
        //失败了
        ElMessage.error(result.msg);
      }
    } else {
      //不通过
      ElMessage.error("Form validation failed");
    }
  });
};
//表单引用
const empFormRef = ref();

//表单校验规则
const rules = ref({
  username: [
    { required: true, message: "Please enter your username", trigger: "blur" },
    {
      min: 2,
      max: 20,
      message: "The username length should be between 2 and 20 characters.",
      trigger: "blur",
    },
  ],
  name: [
    { required: true, message: "Please enter your name", trigger: "blur" },
    {
      min: 2,
      max: 10,
      message: "The name length should be between 2 and 10 characters.",
      trigger: "blur",
    },
  ],
  gender: [
    { required: true, message: "Please select your gender", trigger: "change" },
  ],
  phone: [
    {
      required: true,
      message: "Please enter your phone number",
      trigger: "blur",
    },
    /**
     * 正则表达式: / ..... / ;  ^ : 以...开始 ;  $ : 以 ... 结束
     * [3-9] : 范围 3-9 之间
     * \d : 数字, [0-9]
     * {9} : 量词
     */
    {
      pattern: /^1[3-9]\d{9}$/,
      message: "Please enter a valid mobile number",
      trigger: "blur",
    },
  ],
});
</script>

<template>
  <h1>Employee Management</h1>

  <!-- 搜索栏 -->
  <div class="container">
    <el-form :inline="true" :model="searchEmp" class="demo-form-inline">
      <el-form-item label="Name">
        <el-input
          v-model="searchEmp.name"
          placeholder="Please enter the employee's name"
        />
      </el-form-item>

      <el-form-item label="Gender">
        <el-select v-model="searchEmp.gender" placeholder="Please select">
          <el-option label="male" value="1" />
          <el-option label="female" value="2" />
        </el-select>
      </el-form-item>

      <el-form-item label="Date of entry">
        <el-date-picker
          v-model="searchEmp.date"
          type="daterange"
          range-separator="to"
          start-placeholder="Start Date"
          end-placeholder="End Date"
          value-format="YYYY-MM-DD"
        />
      </el-form-item>

      <el-form-item>
        <el-button type="primary" @click="search">Search</el-button>
        <el-button type="info" @click="clear">Clear</el-button>
      </el-form-item>
    </el-form>
  </div>

  <!-- 功能按钮 -->
  <div class="container">
    <el-button type="primary" @click="addEmp">+ Add Employee</el-button>
    <el-button type="danger" @click="">- Batch Delete</el-button>
  </div>

  <!-- 数据展示表格 -->
  <div class="container">
    <el-table :data="empList" border style="width: 100%">
      <el-table-column type="selection" width="55" align="center" />
      <el-table-column prop="name" label="name" width="120" align="center" />
      <el-table-column label="gender" width="120" align="center">
        <template #default="scope">
          {{ scope.row.gender == 1 ? "male" : "female" }}
        </template>
      </el-table-column>
      <el-table-column label="Avatar" width="120" align="center">
        <template #default="scope">
          <img :src="scope.row.image" height="30px" />
        </template>
      </el-table-column>
      <el-table-column
        prop="deptName"
        label="Department"
        width="120"
        align="center"
      />
      <el-table-column prop="job" label="Position" width="120" align="center">
        <template #default="scope">
          <span v-if="scope.row.job == 1">班主任</span>
          <span v-else-if="scope.row.job == 2">讲师</span>
          <span v-else-if="scope.row.job == 3">学工主管</span>
          <span v-else-if="scope.row.job == 4">教研主管</span>
          <span v-else-if="scope.row.job == 5">咨询师</span>
          <span v-else>其他</span>
        </template>
      </el-table-column>
      <el-table-column
        prop="entryDate"
        label="Date of entry"
        width="180"
        align="center"
      />
      <el-table-column
        prop="updateTime"
        label="Last operation time"
        width="200"
        align="center"
      />
      <el-table-column label="operation" align="center">
        <template #default="scope">
          <el-button type="primary" size="small" @click=""
            ><el-icon><EditPen /></el-icon> Edit</el-button
          >
          <el-button type="danger" size="small" @click=""
            ><el-icon><Delete /></el-icon> Delete</el-button
          >
        </template>
      </el-table-column>
    </el-table>
  </div>

  <!-- 分页条 -->
  <div class="container">
    <el-pagination
      v-model:current-page="currentPage"
      v-model:page-size="pageSize"
      :page-sizes="[5, 10, 20, 30, 50, 75, 100]"
      :background="background"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total"
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
    />
  </div>

  <!-- 新增员工/修改员工的对话框 -->
  <el-dialog v-model="dialogVisible" :title="dialogTitle">
    <el-form
      :model="employee"
      :rules="rules"
      ref="empFormRef"
      label-width="80px"
    >
      <!-- 基本信息 -->
      <!-- 第一行 -->
      <el-row :gutter="20">
        <el-col :span="12">
          <el-form-item label="username" prop="username">
            <el-input
              v-model="employee.username"
              placeholder="Please enter the employee username, 2-20 characters."
            ></el-input>
          </el-form-item>
        </el-col>

        <el-col :span="12">
          <el-form-item label="name" prop="name">
            <el-input
              v-model="employee.name"
              placeholder="Please enter the employee's name, 2-10 characters."
            ></el-input>
          </el-form-item>
        </el-col>
      </el-row>

      <!-- 第二行 -->
      <el-row :gutter="20">
        <el-col :span="12">
          <el-form-item label="gender" prop="gender">
            <el-select
              v-model="employee.gender"
              placeholder="Please select your gender"
              style="width: 100%"
            >
              <el-option
                v-for="g in genders"
                :key="g.value"
                :label="g.name"
                :value="g.value"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-col>

        <el-col :span="12">
          <el-form-item label="phone" prop="phone">
            <el-input
              v-model="employee.phone"
              placeholder="Please enter the employee's mobile phone number"
            ></el-input>
          </el-form-item>
        </el-col>
      </el-row>

      <!-- 第三行 -->
      <el-row :gutter="20">
        <el-col :span="12">
          <el-form-item label="Position">
            <el-select
              v-model="employee.job"
              placeholder="Please select a position"
              style="width: 100%"
            >
              <el-option
                v-for="j in jobs"
                :key="j.value"
                :label="j.name"
                :value="j.value"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="Salary">
            <el-input
              v-model="employee.salary"
              placeholder="Please enter employee salary"
            ></el-input>
          </el-form-item>
        </el-col>
      </el-row>

      <!-- 第四行 -->
      <el-row :gutter="20">
        <el-col :span="12">
          <el-form-item label="Department">
            <el-select
              v-model="employee.deptId"
              placeholder="Please select a department"
              style="width: 100%"
            >
              <el-option
                v-for="d in depts"
                :key="d.id"
                :label="d.name"
                :value="d.id"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="12">
          <el-form-item label="Date of joining">
            <el-date-picker
              v-model="employee.entryDate"
              type="date"
              style="width: 100%"
              placeholder="Select date"
              format="YYYY-MM-DD"
              value-format="YYYY-MM-DD"
            ></el-date-picker>
          </el-form-item>
        </el-col>
      </el-row>

      <!-- 第五行 -->
      <el-row :gutter="20">
        <el-col :span="24">
          <el-form-item label="Avatar">
            <el-upload
              class="avatar-uploader"
              action="/api/upload"
              :show-file-list="false"
              :on-success="handleAvatarSuccess"
              :before-upload="beforeAvatarUpload"
            >
              <img v-if="employee.image" :src="employee.image" class="avatar" />
              <el-icon v-else class="avatar-uploader-icon"><Plus /></el-icon>
            </el-upload>
          </el-form-item>
        </el-col>
      </el-row>

      <!-- 工作经历 -->
      <!-- 第六行 -->
      <el-row :gutter="10">
        <el-col :span="24">
          <el-form-item label="Work Experience">
            <el-button type="success" size="small" @click="addExprItem"
              >+ Add work experience</el-button
            >
          </el-form-item>
        </el-col>
      </el-row>

      <!-- 第七行 ...  工作经历 -->
      <el-row :gutter="3" v-for="(expr, index) in employee.exprList">
        <el-col :span="10">
          <el-form-item size="small" label="时间" label-width="80px">
            <el-date-picker
              type="daterange"
              v-model="expr.exprDate"
              range-separator="to"
              start-placeholder="Start Date"
              end-placeholder="End Date"
              format="YYYY-MM-DD"
              value-format="YYYY-MM-DD"
            ></el-date-picker>
          </el-form-item>
        </el-col>

        <el-col :span="6">
          <el-form-item size="small" label="Company" label-width="60px">
            <el-input
              placeholder="Please enter the company name"
              v-model="expr.company"
            ></el-input>
          </el-form-item>
        </el-col>

        <el-col :span="6">
          <el-form-item size="small" label="Position" label-width="60px">
            <el-input
              placeholder="Please enter the position"
              v-model="expr.job"
            ></el-input>
          </el-form-item>
        </el-col>

        <el-col :span="2">
          <el-form-item size="small" label-width="0px">
            <el-button type="danger" @click="delExprItem(index)"
              >- Delete</el-button
            >
          </el-form-item>
        </el-col>
      </el-row>
    </el-form>

    <!-- 底部按钮 -->
    <template #footer>
      <span class="dialog-footer">
        <el-button @click="dialogVisible = false">Cancel</el-button>
        <el-button type="primary" @click="save">Save</el-button>
      </span>
    </template>
  </el-dialog>
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
