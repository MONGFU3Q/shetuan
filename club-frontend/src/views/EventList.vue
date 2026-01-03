<template>
  <div>
    <el-card style="margin-bottom: 20px;">
      <div style="display: flex; justify-content: space-between; align-items: center;">
        <h3>📅 活动中心</h3>
        <el-button v-if="role === 'manager'" type="primary" @click="openPublishDialog">
          发布新活动
        </el-button>
      </div>
    </el-card>

    <el-row :gutter="20">
      <el-col :span="8" v-for="item in events" :key="item.id" style="margin-bottom: 20px;">
        <el-card shadow="hover">
          <template #header>
            <div style="display: flex; justify-content: space-between; align-items: center;">
              <div style="font-weight: bold;">{{ item.title }}</div>
              <el-button 
                v-if="role === 'admin' || role === 'manager'" 
                type="danger" link size="small" @click="handleDelete(item.id)">
                删除
              </el-button>
            </div>
          </template>
          
          <p style="color: #666; font-size: 13px;">主办社团: {{ item.clubName }}</p>
          <p style="color: #666; font-size: 13px;">活动地点: {{ item.location || '线上/待定' }}</p>
          <p style="margin-top: 10px;">{{ item.content }}</p>
          
          <div style="margin-top: 15px; text-align: right;">
            <span style="font-size: 12px; color: #999; margin-right: 10px;">
              已报名: {{ item.participantIds.length }} 人
            </span>
            <el-button v-if="role === 'student'" type="primary" size="small" @click="joinEvent(item.id)">
              立即报名
            </el-button>
          </div>
        </el-card>
      </el-col>
    </el-row>

    <el-dialog v-model="dialogVisible" title="发布活动" width="500px">
      <el-form :model="form" label-width="80px">
        <el-form-item label="活动标题">
          <el-input v-model="form.title" placeholder="例如：迎新晚会" />
        </el-form-item>
        
        <el-form-item label="活动地点">
          <el-input v-model="form.location" placeholder="例如：第二体育馆 / 302教室" />
        </el-form-item>

        <el-form-item label="活动内容">
          <el-input type="textarea" v-model="form.content" placeholder="请输入活动详情..." />
        </el-form-item>
        
        <el-form-item label="主办社团">
          <el-select v-model="form.clubId" placeholder="请选择您的社团" style="width: 100%" @change="handleClubChange">
            <el-option
              v-for="club in myClubs"
              :key="club.id"
              :label="club.name"
              :value="club.id"
            />
          </el-select>
        </el-form-item>
      </el-form>
      <template #footer>
        <el-button @click="dialogVisible = false">取消</el-button>
        <el-button type="primary" @click="submitEvent">发布</el-button>
      </template>
    </el-dialog>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import request from '../utils/request'
import { ElMessage, ElMessageBox } from 'element-plus'

const role = localStorage.getItem('role')
// 在data部分添加模拟数据
const events = ref([
  { 
    id: 1, 
    title: '迎新晚会', 
    clubName: '音乐社', 
    content: '欢迎新生加入，有精彩表演', 
    location: '学生活动中心', 
    participantIds: [2, 3] 
  },
  { 
    id: 2, 
    title: '篮球比赛', 
    clubName: '篮球社', 
    content: '校内篮球联赛', 
    location: '体育馆', 
    participantIds: [1, 2] 
  },
  { 
    id: 3, 
    title: '编程讲座', 
    clubName: '计算机协会', 
    content: 'Python入门讲座', 
    location: '计算机楼201', 
    participantIds: [2] 
  },
]);

// 模拟我的社团数据
const myClubs = ref([
  { id: 1, name: '计算机协会' },
  { id: 2, name: '篮球社' },
]);

const dialogVisible = ref(false)
const form = ref({ title: '', content: '', location: '', clubId: null, clubName: '' })

// 修改fetchEvents函数
const fetchEvents = async () => {
  try {
    // 直接使用模拟数据，不发送请求
    console.log('使用模拟活动数据');
  } catch (error) { 
    console.error(error) 
  }
}

// 修改fetchMyManagedClubs函数
const fetchMyManagedClubs = async () => {
  const userId = localStorage.getItem('userId')
  if (!userId) return
  
  try {
    // 直接使用模拟数据
    console.log('使用模拟社团数据');
  } catch (error) { 
    console.error(error) 
  }
}

// 打开弹窗时，自动加载社团列表
const openPublishDialog = () => {
  dialogVisible.value = true
  // 每次打开都重置表单
  form.value = { title: '', content: '', location: '', clubId: null, clubName: '' }
  fetchMyManagedClubs()
}

// 当下拉框选中社团时，自动填充 clubName
const handleClubChange = (val) => {
  const selectedClub = myClubs.value.find(c => c.id === val)
  if (selectedClub) {
    form.value.clubName = selectedClub.name
  }
}

// 修改submitEvent函数
const submitEvent = async () => {
  if (!form.value.clubId) {
    return ElMessage.warning('请选择主办社团')
  }
  
  try {
    // 模拟添加新活动
    const newEvent = {
      id: events.value.length + 1,
      title: form.value.title,
      clubName: form.value.clubName,
      content: form.value.content,
      location: form.value.location,
      participantIds: []
    };
    
    events.value.push(newEvent);
    ElMessage.success('发布成功');
    dialogVisible.value = false;
  } catch (error) { 
    console.error(error) 
  }
}

// 修改joinEvent函数
const joinEvent = async (eventId) => {
  const currentUserId = parseInt(localStorage.getItem('userId') || '0');
  if (!currentUserId) return ElMessage.error('请先登录');
  
  try {
    // 模拟报名
    const event = events.value.find(e => e.id === eventId);
    if (event && !event.participantIds.includes(currentUserId)) {
      event.participantIds.push(currentUserId);
      ElMessage.success('报名成功');
    } else {
      ElMessage.warning('您已报名此活动');
    }
  } catch (error) { 
    console.error(error) 
  }
}

// 修改handleDelete函数
const handleDelete = (eventId) => {
  ElMessageBox.confirm('确定要删除这个活动吗？', '警告', { type: 'warning' })
    .then(async () => {
      try {
        // 模拟删除
        const index = events.value.findIndex(e => e.id === eventId);
        if (index !== -1) {
          events.value.splice(index, 1);
          ElMessage.success('删除成功');
        }
      } catch (error) {}
    })
}

onMounted(fetchEvents)
</script>