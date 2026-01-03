<template>
  <el-card>
    <template #header>
      <h3>📝 建社申请审批 (System Admin)</h3>
    </template>

    <el-table :data="applications" stripe style="width: 100%" v-loading="loading">
      <el-table-column prop="id" label="ID" width="60" />
      <el-table-column prop="applicantName" label="申请人" width="100" />
      <el-table-column prop="name" label="拟定社团名" width="150" />
      <el-table-column prop="category" label="分类" width="100">
        <template #default="scope">
          <el-tag>{{ scope.row.category }}</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="description" label="申请理由" />
      <el-table-column label="操作" width="200">
        <template #default="scope">
          <el-button type="success" size="small" @click="handleApprove(scope.row.id)">通过</el-button>
          <el-button type="danger" size="small" @click="handleReject(scope.row.id)">驳回</el-button>
        </template>
      </el-table-column>
    </el-table>

    <el-empty v-if="applications.length === 0" description="暂无待审核申请" />
  </el-card>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import request from '../utils/request'
import { ElMessage } from 'element-plus'

const applications = ref([
  { id: 1, applicantName: '张三', name: '摄影俱乐部', category: '艺术', description: '热爱摄影，希望找到志同道合的朋友' },
  { id: 2, applicantName: '李四', name: '舞蹈社', category: '体育', description: '推广舞蹈文化，丰富校园生活' },
  { id: 3, applicantName: '王五', name: '书法协会', category: '艺术', description: '传承中华书法文化' },
]);
const loading = ref(false)

const fetchApplications = async () => {
  loading.value = true
  try {
    const res = await request.get('/admin/applications')
    applications.value = res.data
  } catch (error) {
    console.error(error)
  } finally {
    loading.value = false
  }
}

// 修改handleApprove函数
const handleApprove = async (id) => {
  try {
    // 模拟审批通过
    const app = applications.value.find(item => item.id === id);
    if (app) {
      app.status = 'approved';
      ElMessage.success('审批通过');
      
      // 从列表中移除已审批的申请
      applications.value = applications.value.filter(item => item.id !== id);
    }
  } catch (error) {
    console.error(error);
  }
}

// 修改handleReject函数
const handleReject = async (id) => {
  try {
    // 模拟驳回
    const app = applications.value.find(item => item.id === id);
    if (app) {
      app.status = 'rejected';
      ElMessage.warning('已驳回');
      
      // 从列表中移除已驳回的申请
      applications.value = applications.value.filter(item => item.id !== id);
    }
  } catch (error) {
    console.error(error);
  }
}

onMounted(fetchApplications)
</script>