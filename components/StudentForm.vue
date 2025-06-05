<template>
  <el-dialog
    :title="isEdit ? '编辑学生信息' : '添加学生信息'"
    :visible.sync="visible"
    width="50%"
    @close="handleClose"
  >
    <el-form
      :model="formData"
      :rules="rules"
      ref="studentForm"
      label-width="100px"
    >
      <el-form-item label="学号" prop="id">
        <el-input v-model="formData.id" :disabled="isEdit"></el-input>
      </el-form-item>
      <el-form-item label="姓名" prop="name">
        <el-input v-model="formData.name"></el-input>
      </el-form-item>
      <el-form-item label="性别" prop="gender">
        <el-radio-group v-model="formData.gender">
          <el-radio label="male">男</el-radio>
          <el-radio label="female">女</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="年龄" prop="age">
        <el-input-number
          v-model="formData.age"
          :min="15"
          :max="30"
        ></el-input-number>
      </el-form-item>
      <el-form-item label="专业" prop="major">
        <el-input v-model="formData.major"></el-input>
      </el-form-item>
      <el-form-item label="年级" prop="grade">
        <el-select v-model="formData.grade" placeholder="请选择年级">
          <el-option label="大一" value="大一"></el-option>
          <el-option label="大二" value="大二"></el-option>
          <el-option label="大三" value="大三"></el-option>
          <el-option label="大四" value="大四"></el-option>
        </el-select>
      </el-form-item>
    </el-form>
    
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取 消</el-button>
      <el-button type="primary" @click="submitForm">确 定</el-button>
    </span>
  </el-dialog>
</template>

<script>
export default {
  name: 'StudentForm',
  props: {
    visible: {
      type: Boolean,
      default: false
    },
    formData: {
      type: Object,
      default: () => ({})
    },
    isEdit: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      rules: {
        id: [
          { required: true, message: '请输入学号', trigger: 'blur' },
          { pattern: /^\d{4,10}$/, message: '学号必须是4-10位数字', trigger: 'blur' }
        ],
        name: [
          { required: true, message: '请输入姓名', trigger: 'blur' },
          { min: 2, max: 10, message: '长度在 2 到 10 个字符', trigger: 'blur' }
        ],
        gender: [
          { required: true, message: '请选择性别', trigger: 'change' }
        ],
        age: [
          { required: true, message: '请输入年龄', trigger: 'blur' },
          { type: 'number', min: 15, max: 30, message: '年龄必须在15到30岁之间', trigger: 'blur' }
        ],
        major: [
          { required: true, message: '请输入专业', trigger: 'blur' }
        ],
        grade: [
          { required: true, message: '请选择年级', trigger: 'change' }
        ]
      }
    }
  },
  methods: {
    submitForm() {
      this.$refs.studentForm.validate(valid => {
        if (valid) {
          this.$emit('submit', { ...this.formData })
          this.$emit('update:visible', false)
        } else {
          return false
        }
      })
    },
    handleClose() {
      this.$refs.studentForm.resetFields()
      this.$emit('update:visible', false)
    }
  },
  watch: {
    visible(val) {
      if (!val) {
        this.$refs.studentForm.resetFields()
      }
    }
  }
}
</script>