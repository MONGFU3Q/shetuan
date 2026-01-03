<template>
  <div class="login-bg">
    <el-card class="login-card">
      <template #header>
        <div class="card-header">
          <h2>社团管理系统</h2>
        </div>
      </template>
      <el-form :model="form" label-width="0">
        <el-form-item>
          <el-input v-model="form.username" placeholder="请输入用户名" :prefix-icon="User" />
        </el-form-item>
        <el-form-item>
          <el-input type="password" v-model="form.password" placeholder="请输入密码" :prefix-icon="Lock" show-password />
        </el-form-item>
        <el-button type="primary" :loading="loading" @click="handleLogin" style="width: 100%; margin-top: 10px;">
          登录
        </el-button>

        <div style="margin-top: 10px; text-align: center;">
    <el-button link type="primary" @click="$router.push('/register')">没有账号？去注册</el-button>
</div>
      </el-form>
      <div class="tips">
        
      </div>
    </el-card>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import request from '../utils/request'
import { ElMessage } from 'element-plus'
import { User, Lock } from '@element-plus/icons-vue'

const router = useRouter()
const loading = ref(false)
const form = ref({ username: '', password: '' })

// 在script部分修改handleLogin函数
const handleLogin = async () => {
  if(!form.value.username || !form.value.password) return ElMessage.warning('请输入账号密码')
  
  loading.value = true
  
  try {
    // 使用模拟登录（不再真正请求后端）
    const mockUsers = [
      { username: 'admin', password: 'admin123', role: 'admin', userId: 1 },
      { username: 'student1', password: '123456', role: 'student', userId: 2 },
      { username: 'manager1', password: '123456', role: 'manager', userId: 3 },
    ];
    
    const user = mockUsers.find(u => 
      u.username === form.value.username && u.password === form.value.password
    );
    
    if (user) {
      // 模拟生成token
      const mockToken = 'mock-jwt-token-' + Date.now();
      
      // 保存到localStorage
      localStorage.setItem('token', mockToken);
      localStorage.setItem('role', user.role);
      localStorage.setItem('username', user.username);
      localStorage.setItem('userId', user.userId.toString());
      
      ElMessage.success('登录成功');
      router.push('/home');
    } else {
      ElMessage.error('用户名或密码错误');
    }
  } catch (err) {
    console.error(err);
    ElMessage.error('登录失败');
  } finally {
    loading.value = false;
  }
}
</script>

<style scoped>
.login-bg {
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}
.login-card { width: 380px; border-radius: 10px; }
.card-header h2 { text-align: center; margin: 0; color: #333; }
.tips { margin-top: 20px; font-size: 12px; color: #999; text-align: center; }
</style>
