<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="学生ID" prop="studentId">
      <el-input v-model="dataForm.studentId" placeholder="学生ID"></el-input>
    </el-form-item>
    <el-form-item label="班课ID" prop="courseId">
      <el-input v-model="dataForm.courseId" placeholder="班课ID"></el-input>
    </el-form-item>
    <el-form-item label="签到时间" prop="signTime">
      <el-input v-model="dataForm.signTime" placeholder="签到时间"></el-input>
    </el-form-item>
    <el-form-item label="签到IP地址" prop="ipaddr">
      <el-input v-model="dataForm.ipaddr" placeholder="签到IP地址"></el-input>
    </el-form-item>
    <el-form-item label="备注" prop="remark">
      <el-input v-model="dataForm.remark" placeholder="备注"></el-input>
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
        dataForm: {
          studentSignId: 0,
          studentId: '',
          courseId: '',
          signTime: '',
          ipaddr: '',
          remark: ''
        },
        dataRule: {
          studentId: [
            { required: true, message: '学生ID不能为空', trigger: 'blur' }
          ],
          courseId: [
            { required: true, message: '班课ID不能为空', trigger: 'blur' }
          ],
          signTime: [
            { required: true, message: '签到时间不能为空', trigger: 'blur' }
          ],
          ipaddr: [
            { required: true, message: '签到IP地址不能为空', trigger: 'blur' }
          ],
          remark: [
            { required: true, message: '备注不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.studentSignId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.studentSignId) {
            this.$http({
              url: this.$http.adornUrl(`/generator/clastudentsign/info/${this.dataForm.studentSignId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.studentId = data.claStudentSign.studentId
                this.dataForm.courseId = data.claStudentSign.courseId
                this.dataForm.signTime = data.claStudentSign.signTime
                this.dataForm.ipaddr = data.claStudentSign.ipaddr
                this.dataForm.remark = data.claStudentSign.remark
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
              url: this.$http.adornUrl(`/generator/clastudentsign/${!this.dataForm.studentSignId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'studentSignId': this.dataForm.studentSignId || undefined,
                'studentId': this.dataForm.studentId,
                'courseId': this.dataForm.courseId,
                'signTime': this.dataForm.signTime,
                'ipaddr': this.dataForm.ipaddr,
                'remark': this.dataForm.remark
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
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
