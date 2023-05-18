<template>
  <div class="table-box">
    <!-- 标题 -->
    <div class="title">
      <h2>CRUD</h2>
    </div>
    <!-- 搜索 -->
    <div class="query-box">
      <el-input
        v-model="queryIpt"
        placeholder="请搜索姓名"
        class="query-ipt"
        @input="handleQueryName"
      ></el-input>
      <div class="btn-list">
        <el-button
          type="danger"
          size="default"
          v-if="multipleSelection.length > 0"
          @click="handleDeleteAll"
          >删除选中</el-button
        >
        <el-button type="primary" size="default" @click="handleAdd"
          >增加</el-button
        >
      </div>
    </div>
    <!-- 表格 -->
    <el-table
      ref="multipleTableRef"
      :data="tableData"
      style="width: 80%"
      border
      @selection-change="handleSelectionChange"
    >
      <el-table-column type="selection" width="55" />
      <el-table-column prop="name" label="姓名" width="120" />
      <el-table-column prop="phone" label="电话" width="120" />
      <el-table-column prop="email" label="邮箱" width="120" />
      <el-table-column prop="state" label="状态" width="120" />
      <el-table-column prop="address" label="地址" width="300" />
      <el-table-column fixed="right" label="操作" width="120">
        <template #default="scope">
          <el-button
            link
            type="danger"
            size="small"
            @click="handleDelete(scope.row.id)"
            >删除</el-button
          >
          <el-button
            @click="handleEdit(scope.row)"
            link
            type="primary"
            size="small"
            >编辑</el-button
          >
        </template>
      </el-table-column>
    </el-table>

    <!-- dialog弹窗 -->
    <el-dialog
      v-model="dialogFormVisible"
      :title="dialogTitle == 'add' ? '新增' : '编辑'"
    >
      <el-form :model="tableForm">
        <el-form-item label="姓名" :label-width="80">
          <el-input v-model="tableForm.name" autocomplete="off" />
        </el-form-item>
        <el-form-item label="电话" :label-width="80">
          <el-input v-model="tableForm.phone" autocomplete="off" />
        </el-form-item>
        <el-form-item label="邮箱" :label-width="80">
          <el-input v-model="tableForm.email" autocomplete="off" />
        </el-form-item>
        <el-form-item label="状态" :label-width="80">
          <el-input v-model="tableForm.state" autocomplete="off" />
        </el-form-item>
        <el-form-item label="地址" :label-width="80">
          <el-input v-model="tableForm.address" autocomplete="off" />
        </el-form-item>
      </el-form>
      <template #footer>
        <span class="dialog-footer">
          <el-button @click="dialogFormVisible = false">取消</el-button>
          <el-button type="primary" @click="handleConfirm"> 确认 </el-button>
        </span>
      </template>
    </el-dialog>
  </div>
</template>

<script setup>
import { reactive, ref } from "vue";
import { ElMessage } from "element-plus";
const queryIpt = ref("");
const multipleTableRef = ref("");

const multipleSelection = ref([]);

const dialogFormVisible = ref(false);
const dialogTitle = ref("");

const tableForm = ref({
  name: "张三",
  phone: "18907051305",
  email: "862@qq.com",
  state: "在职",
  address: "广东省",
});
const tableData = ref([
  {
    id: "1",
    name: "Tom1",
    phone: "18907051305",
    email: "862@qq.com",
    state: "在职",
    address: "No. 190,Grove St,Los Angeles",
  },
  {
    id: "2",
    name: "Tom2",
    phone: "18907051305",
    email: "862@qq.com",
    state: "在职",
    address: "No. 190,Grove St,Los Angeles",
  },
  {
    id: "3",
    name: "Tom3",
    phone: "18907051305",
    email: "862@qq.com",
    state: "在职",
    address: "No. 190,Grove St,Los Angeles",
  },
  {
    id: "4",
    name: "Tom4",
    phone: "18907051305",
    email: "862@qq.com",
    state: "在职",
    address: "No. 190,Grove St,Los Angeles",
  },
]);
const tableDataCopy = Object.assign(tableData.value)

// 搜素
const handleQueryName = (val) => {
  if (val.length > 0) {
    tableData.value = tableData.value.filter(item => (item.name).toLowerCase().match(val.toLowerCase()))
  } else {
    tableData.value = tableDataCopy
  }
  
}

// 添加数据
const handleAdd = () => {
  dialogFormVisible.value = true;
  dialogTitle.value = "add";
  tableForm.value = {};
};
// 编辑数据
const handleEdit = (row) => {
  console.log(row);
  dialogFormVisible.value = true;
  dialogTitle.value = "edit";
  tableForm.value = { ...row };
};
// 删除单条
const handleDelete = (id) => {
  const index = tableData.value.findIndex((item) => item.id === id);
  tableData.value.splice(index, 1);
};
// 删除选中
const handleDeleteAll = () => {
  multipleSelection.value.forEach((id) => {
    handleDelete(id);
  });
  multipleSelection.value = [];
};

// 选中
const handleSelectionChange = (val) => {
  // multipleSelection.value = val;
  multipleSelection.value = [];
  val.forEach((item) => {
    multipleSelection.value.push(item.id);
  });
  // console.log(multipleSelection.value)
};

// 确认添加数据
const handleConfirm = () => {
  dialogFormVisible.value = false;
  // 判断是新增还是编辑操作
  if (dialogTitle.value === "add") {
    tableData.value.push({
      id: (tableData.value.length + 1).toString(),
      ...tableForm.value,
    });
    ElMessage({
      message: "新增成功.",
      type: "success",
    });
  } else {
    // 编辑： 找到当前索引
    let index = tableData.value.findIndex(
      (item) => item.id === tableForm.value.id
    );
    // 根据索引替换对应的数据
    tableData.value[index] = tableForm.value;
    ElMessage({
      message: "编辑成功.",
      type: "success",
    });
  }
};
</script>

<style scoped>
.table-box {
  width: 800px;
  margin: 60px auto;
}
.title {
  text-align: center;
}
.query-box {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20px;
}
.query-ipt {
  width: 200px;
}
</style>
