<template>
  <el-card>
    <template #header><h3>用户管理 (System Admin)</h3></template>
    <el-table :data="users" stripe>
      <el-table-column prop="id" label="ID" width="50" />
      <el-table-column prop="username" label="用户名" />
      <el-table-column prop="role" label="角色">
        <template #default="scope">
          <el-tag :type="scope.row.role === 'admin' ? 'danger' : 'primary'">{{ scope.row.role }}</el-tag>
        </template>
      </el-table-column>
      <el-table-column prop="banned" label="状态">
        <template #default="scope">
          <el-tag :type="scope.row.banned ? 'danger' : 'success'">
            {{ scope.row.banned ? '已封禁' : '正常' }}
          </el-tag>
        </template>
      </el-table-column>
      <el-table-column label="操作">
        <template #default="scope">
          <el-button 
            size="small" 
            :type="scope.row.banned ? 'success' : 'danger'" 
            @click="toggleBan(scope.row)"
            v-if="scope.row.role !== 'admin'"
          >
            {{ scope.row.banned ? '解封' : '封号' }}
          </el-button>
        </template>
      </el-table-column>
    </el-table>
  </el-card>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import request from '../utils/request'
import { ElMessage } from 'element-plus'

const users = ref([
  { id: 1, username: 'admin', role: 'admin', banned: false },
  { id: 2, username: 'student1', role: 'student', banned: false },
  { id: 3, username: 'manager1', role: 'manager', banned: true },
  { id: 4, username: 'student2', role: 'student', banned: false },
]);

const fetchUsers = async () => {
  const res = await request.get('/admin/users')
  users.value = res.data
}

const toggleBan = async (row) => {
  // 模拟封禁/解封
  const user = users.value.find(u => u.id === row.id);
  if (user && user.role !== 'admin') {
    user.banned = !user.banned;
    ElMessage.success('操作成功');
  }
}

onMounted(fetchUsers)
</script>