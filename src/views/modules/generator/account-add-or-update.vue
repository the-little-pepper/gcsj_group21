<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户表id" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="用户表id" :disabled="dataForm.id !== 0"></el-input>
    </el-form-item>
    <el-form-item label="账号类型" prop="type">
      <el-radio-group  v-model="dataForm.type" :disabled="dataForm.id !== 0">
        <el-radio v-for="(name, i) in acctypes" class="radio" :label="i" :key="i">{{name}}</el-radio>
      </el-radio-group>
    </el-form-item>
    <el-form-item label="用户名" prop="userName">
      <el-input v-model="dataForm.userName" placeholder="用户名"></el-input>
    </el-form-item>
    <el-form-item label="密码" prop="password">
      <el-input v-model="dataForm.password" placeholder="密码"></el-input>
    </el-form-item>
    <el-form-item label="状态" prop="status">
      <el-radio-group  v-model="dataForm.status">
        <el-radio v-for="(name, i) in statustypes" class="radio" :label="i" :key="i">{{name}}</el-radio>
      </el-radio-group>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        acctypes: {1: '手机号', 2: '邮箱', 3: '用户名'},
        statustypes: {1: '正常', 0: '禁用'},
        dataForm: {
          id: 0,
          userId: '',
          type: '',
          userName: '',
          password: '',
          status: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '用户表id不能为空', trigger: 'blur' }
          ],
          type: [
            { required: true, message: '账号类型不能为空', trigger: 'blur' }
          ],
          userName: [
            { required: true, message: '用户名包括邮箱手机号等不能为空', trigger: 'blur' }
          ],
          password: [
            { required: true, message: '密码不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '禁用标志不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/generator/account/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 200) {
                this.dataForm.userId = data.account.userId
                this.dataForm.type = data.account.type.toString()
                this.dataForm.userName = data.account.userName
                this.dataForm.password = data.account.password
                this.dataForm.status = data.account.status.toString()
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/generator/account/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'type': this.dataForm.type,
                'userName': this.dataForm.userName,
                'password': this.dataForm.password,
                'status': this.dataForm.status
              })
            }).then(({data}) => {
              if (data && data.code === 200) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
