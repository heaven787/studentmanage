<template>
  <div id="app" class="app-container">
    <el-container>
      <el-header>
        <h1>学生信息管理系统</h1>
      </el-header>
      
      <el-main>
        <div class="toolbar">
          <el-button type="primary" @click="showAddDialog">添加学生</el-button>
          <el-input
            v-model="searchQuery"
            placeholder="搜索学生..."
            style="width: 300px; margin-left: 20px;"
            clearable
            @clear="handleSearchClear"
            @keyup.enter.native="handleSearch"
          >
            <el-button slot="append" icon="el-icon-search" @click="handleSearch"></el-button>
          </el-input>
        </div>
        
        <el-table
          :data="filteredStudents"
          border
          style="width: 100%"
          v-loading="loading"
        >
          <el-table-column prop="id" label="学号" width="120"></el-table-column>
          <el-table-column prop="name" label="姓名" width="120"></el-table-column>
          <el-table-column prop="gender" label="性别" width="80">
            <template slot-scope="scope">
              {{ scope.row.gender === 'male' ? '男' : '女' }}
            </template>
          </el-table-column>
          <el-table-column prop="age" label="年龄" width="80"></el-table-column>
          <el-table-column prop="major" label="专业"></el-table-column>
          <el-table-column prop="grade" label="年级" width="100"></el-table-column>
          <el-table-column label="操作" width="180">
            <template slot-scope="scope">
              <el-button
                size="mini"
                @click="handleEdit(scope.$index, scope.row)"
              >编辑</el-button>
              <el-button
                size="mini"
                type="danger"
                @click="handleDelete(scope.$index, scope.row)"
              >删除</el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-main>
    </el-container>
    
    <!-- 添加/编辑学生对话框 -->
    <student-form
      :visible.sync="dialogVisible"
      :form-data="currentStudent"
      :is-edit="isEdit"
      @submit="handleSubmit"
    ></student-form>
  </div>
</template>

<script>
import StudentForm from './components/StudentForm.vue'

export default {
  name: 'App',
  components: {
    StudentForm
  },
  data() {
    return {
      students: [
        { id: '1001', name: '张三', gender: 'male', age: 20, major: '计算机科学', grade: '大三' },
        { id: '1002', name: '李四', gender: 'male', age: 21, major: '软件工程', grade: '大四' },
        { id: '1003', name: '王五', gender: 'female', age: 19, major: '人工智能', grade: '大二' },
        { id: '1004', name: '赵六', gender: 'female', age: 22, major: '数据科学', grade: '大四' }
      ],
      searchQuery: '',
      loading: false,
      dialogVisible: false,
      currentStudent: this.getEmptyStudent(),
      isEdit: false
    }
  },
  computed: {
    filteredStudents() {
      if (!this.searchQuery) {
        return this.students
      }
      const query = this.searchQuery.toLowerCase()
      return this.students.filter(student => {
        return (
          student.id.toLowerCase().includes(query) ||
          student.name.toLowerCase().includes(query) ||
          student.major.toLowerCase().includes(query) ||
          student.grade.toLowerCase().includes(query)
      )})
    }
  },
  methods: {
    getEmptyStudent() {
      return {
        id: '',
        name: '',
        gender: 'male',
        age: '',
        major: '',
        grade: ''
      }
    },
    showAddDialog() {
      this.currentStudent = this.getEmptyStudent()
      this.isEdit = false
      this.dialogVisible = true
    },
    handleEdit(index, row) {
      this.currentStudent = { ...row }
      this.isEdit = true
      this.dialogVisible = true
    },
    handleDelete(index, row) {
      this.$confirm('确定要删除该学生信息吗?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.students = this.students.filter(student => student.id !== row.id)
        this.$message({
          type: 'success',
          message: '删除成功!'
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
      })
    },
    handleSubmit(studentData) {
      if (this.isEdit) {
        // 更新学生信息
        const index = this.students.findIndex(s => s.id === studentData.id)
        if (index !== -1) {
          this.$set(this.students, index, studentData)
          this.$message.success('学生信息更新成功')
        }
      } else {
        // 检查学号是否已存在
        if (this.students.some(s => s.id === studentData.id)) {
          this.$message.error('该学号已存在')
          return
        }
        // 添加新学生
        this.students.push(studentData)
        this.$message.success('学生信息添加成功')
      }
      this.dialogVisible = false
    },
    handleSearch() {
      // 搜索功能已在计算属性中实现
    },
    handleSearchClear() {
      this.searchQuery = ''
    }
  }
}
</script>

<style>
.app-container {
  padding: 20px;
}
.el-header {
  background-color: #409EFF;
  color: white;
  text-align: center;
  line-height: 60px;
}
.toolbar {
  margin-bottom: 20px;
}
</style>