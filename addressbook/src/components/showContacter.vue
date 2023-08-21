<template>
  <div class="div">
    <el-table :data="tableData" height="500" style="width: 100%" border v-if="renderComponent">
      <el-table-column prop="name" label="姓名" width="80" align="center" show-overflow-tooltip />
      <el-table-column prop="sex" label="性别" width="60" align="center" show-overflow-tooltip/>
      <el-table-column prop="student_id" label="学号" width="120" align="center" show-overflow-tooltip/>
      <el-table-column prop="phone_num" label="电话号码" width="150" align="center" show-overflow-tooltip/>
      <el-table-column prop="major" label="专业" width="130" align="center" show-overflow-tooltip/>
      <el-table-column prop="blacklist" label="黑名单" width="70" align="center" show-overflow-tooltip/>
      <el-table-column label="操作" align="center">
        <template #default="scope">
            <el-button type="danger" @click="handleRowDelete(scope.row.id , scope.$index)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script lang="ts" setup>
import { ElNotification } from "element-plus";
import {ref , onMounted, h, nextTick} from "vue";
import addService from "../apis/addService.ts";
import userStore from "../stores/userStore.ts";

const newUserStore = userStore();
const tableData = ref([]);

onMounted(async () => {
  const owner_id= newUserStore.userSession.id;
  console.log(owner_id);
  const res = await addService.show(owner_id);

  if (res.data.code === 200 && res.data.msg === "OK") {
    tableData.value = res.data.data.contact_list;
  }
  else if (res.data.code === 200501 && res.data.msg === "参数错误") {
    ElNotification({
      title: "失败",
      message: h("i", { style: "color: teal" }, "参数错误！"),
    });
  }
  else if (res.data.code === 200506 && res.data.msg === "联系人列表为空") {
    ElNotification({
      title: "提示",
      message: h("i", { style: "color: teal" }, "联系人为空！"),
    });
  }
  else {
    ElNotification({
      title: "失败",
      message: h("i", { style: "color: teal" }, "网络错误！"),
    });
  }
});

const renderComponent = ref(true);
const forceRender = async () => {
  // 这里移除组件 MyComponent
  renderComponent.value = false;

  // 等待下一次 DOM 更新
  await nextTick();

  // 添加组件
  renderComponent.value = true;
};

const handleRowDelete = async (id:number, index: number) => {
  const res = await addService.delete({id: id});
  if (res.data.code === 200) {
    tableData.value.splice(index, 1);
    ElNotification({
      title: "成功",
      message: h("i", { style: "color: teal" }, "删除成功！"),
    });

    const owner_id= newUserStore.userSession.id;
    console.log(owner_id);
    const res = await addService.show(owner_id);

    if (res.data.code === 200 && res.data.msg === "OK") {
      tableData.value = res.data.data.contact_list;
    }
    else if (res.data.code === 200501 && res.data.msg === "参数错误") {
      ElNotification({
        title: "失败",
        message: h("i", { style: "color: teal" }, "参数错误！"),
      });
    }
    else if (res.data.code === 200506 && res.data.msg === "联系人列表为空") {
      ElNotification({
        title: "提示",
        message: h("i", { style: "color: teal" }, "联系人为空！"),
      });
    }
    else {
      ElNotification({
        title: "失败",
        message: h("i", { style: "color: teal" }, "网络错误！"),
      });
    }
    await forceRender();
  }
  else {
    ElNotification({
      title: "失败",
      message: h("i", { style: "color: teal" }, "删除失败！"),
    });
  }
};
</script>

<style scoped>
  .div {
    width: 700px;
    height: 300px;
    margin: 50px -20px 0 auto;
  }
</style>