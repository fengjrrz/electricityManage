<template>
  <el-dialog
    :title="title"
    :visible="visible"
    width="450px"
    top="80px"
    :before-close="finalClose"
    @close="dialogClose"
    @open="dialogOpen"
  >
    <el-form ref="formRef" inline :model="formData" label-width="90px" :rules="formRules">
      <el-form-item label="名字" prop="name">
        <el-input v-model="formData.name" size="mini" />
      </el-form-item>
      <el-form-item label="手机号" prop="phone">
        <el-input v-model="formData.phone" size="mini" />
      </el-form-item>
      <el-form-item label="邮箱" prop="email">
        <el-input v-model="formData.email" size="mini" />
      </el-form-item>
      <el-form-item label="地址" prop="address">
        <el-input v-model="formData.address" size="mini" />
      </el-form-item>
    </el-form>
    <template slot="footer">
      <el-button size="mini" @click="finalClose">取消</el-button>
      <el-button type="primary" size="mini" :loading="saveLoading" @click="saveModify">保存</el-button>
    </template>
  </el-dialog>
</template>

<script>
import { modifyUser } from '@/api/user.js'
export default {
  props: {
    visible: {
      type: Boolean,
      default: false
    },
    type: {
      type: String,
      default: ''
    },
    rowInfo: {
      type: Object,
      default: () => ({})
    }
  },
  data() {
    return {
      formData: {
        name: '',
        phone: '',
        email: '',
        address: ''
      },
      saveLoading: false,
      formRules: {
        name: [
          { required: true, message: '请输入姓名', trigger: 'blur' }
        ],
        phone: [
          { required: true, message: '请输入手机号', trigger: 'blur' }
        ],
        email: [
          { required: true, message: '请输入邮箱', trigger: 'blur' }
        ],
        address: [
          { required: true, message: '请输入地址', trigger: 'blur' }]
      }
    }
  },
  computed: {
    title() {
      if (this.type === 'edit') {
        return '编辑用户'
      }
      return '新增用户'
    }
  },
  methods: {
    dialogOpen() {
      if (this.type === 'edit') {
        this.formData = this.rowInfo
      }
      this.$refs.formRef.clearValidate()
    },
    finalClose() {
      this.$emit('update:visible', false)
    },
    dialogClose() {
      this.formData = {
        name: '',
        phone: '',
        email: '',
        address: ''
      }
      this.saveLoading = false
    },
    saveModify() {
      this.$refs.formRef.validate(valid => {
        if (valid) {
          this.saveLoading = true
          modifyUser(this.formData).then(res => {
            if (res.code !== 200) return
            this.$message({
              message: `${this.title}成功`,
              type: 'success'
            })
            this.finalClose()
            this.$emit('refetchTable')
          }).finally(() => {
            this.saveLoading = false
          })
        }
      })
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
