<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="教师ID" prop="teacherId">
      <el-input v-model="dataForm.teacherId" placeholder="教师ID"></el-input>
    </el-form-item>
    <el-form-item label="班课ID" prop="courseId">
      <el-input v-model="dataForm.courseId" placeholder="班课ID"></el-input>
    </el-form-item>
    <el-form-item label="开始时间" prop="startTime">
      <el-input v-model="dataForm.startTime" placeholder="开始时间"></el-input>
    </el-form-item>
    <el-form-item label="结束时间" prop="stopTime">
      <el-input v-model="dataForm.stopTime" placeholder="结束时间"></el-input>
    </el-form-item>
    <el-form-item label="发起签到IP地址" prop="ipaddr">
      <el-input v-model="dataForm.ipaddr" placeholder="发起签到IP地址"></el-input>
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
          teacherSignId: 0,
          teacherId: '',
          courseId: '',
          startTime: '',
          stopTime: '',
          ipaddr: '',
          remark: ''
        },
        dataRule: {
          teacherId: [
            { required: true, message: '教师ID不能为空', trigger: 'blur' }
          ],
          courseId: [
            { required: true, message: '班课ID不能为空', trigger: 'blur' }
          ],
          startTime: [
            { required: true, message: '开始时间不能为空', trigger: 'blur' }
          ],
          stopTime: [
            { required: true, message: '结束时间不能为空', trigger: 'blur' }
          ],
          ipaddr: [
            { required: true, message: '发起签到IP地址不能为空', trigger: 'blur' }
          ],
          remark: [
            { required: true, message: '备注不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.teacherSignId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.teacherSignId) {
            this.$http({
              url: this.$http.adornUrl(`/generator/clateachersign/info/${this.dataForm.teacherSignId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.teacherId = data.claTeacherSign.teacherId
                this.dataForm.courseId = data.claTeacherSign.courseId
                this.dataForm.startTime = data.claTeacherSign.startTime
                this.dataForm.stopTime = data.claTeacherSign.stopTime
                this.dataForm.ipaddr = data.claTeacherSign.ipaddr
                this.dataForm.remark = data.claTeacherSign.remark
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
              url: this.$http.adornUrl(`/generator/clateachersign/${!this.dataForm.teacherSignId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'teacherSignId': this.dataForm.teacherSignId || undefined,
                'teacherId': this.dataForm.teacherId,
                'courseId': this.dataForm.courseId,
                'startTime': this.dataForm.startTime,
                'stopTime': this.dataForm.stopTime,
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
