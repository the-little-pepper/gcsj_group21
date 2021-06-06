<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="老师发起签到的表id" prop="taskId">
      <el-input v-model="dataForm.taskId" placeholder="老师发起签到的表id"></el-input>
    </el-form-item>
    <el-form-item label="学生id" prop="studentId">
      <el-input v-model="dataForm.studentId" placeholder="学生id"></el-input>
    </el-form-item>
    <el-form-item label="课程id" prop="courseId">
      <el-input v-model="dataForm.courseId" placeholder="课程id"></el-input>
    </el-form-item>
    <el-form-item label="签到类型" prop="type">
      <el-input v-model="dataForm.type" placeholder="签到类型"></el-input>
    </el-form-item>
    <el-form-item label="经度" prop="longitude">
      <el-input v-model="dataForm.longitude" placeholder="经度"></el-input>
    </el-form-item>
    <el-form-item label="纬度" prop="latitude">
      <el-input v-model="dataForm.latitude" placeholder="纬度"></el-input>
    </el-form-item>
    <el-form-item label="经验值" prop="experience">
      <el-input v-model="dataForm.experience" placeholder="经验值"></el-input>
    </el-form-item>
    <el-form-item label="签到时间" prop="signTime">
      <el-input v-model="dataForm.signTime" placeholder="签到时间"></el-input>
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
          id: 0,
          taskId: '',
          studentId: '',
          courseId: '',
          type: '',
          longitude: '',
          latitude: '',
          experience: '',
          signTime: '',
          remark: ''
        },
        dataRule: {
          taskId: [
            { required: true, message: '老师发起签到的表id不能为空', trigger: 'blur' }
          ],
          studentId: [
            { required: true, message: '学生id不能为空', trigger: 'blur' }
          ],
          courseId: [
            { required: true, message: '课程id不能为空', trigger: 'blur' }
          ],
          type: [
            { required: true, message: '签到类型不能为空', trigger: 'blur' }
          ],
          longitude: [
            { required: true, message: '经度不能为空', trigger: 'blur' }
          ],
          latitude: [
            { required: true, message: '纬度不能为空', trigger: 'blur' }
          ],
          experience: [
            { required: true, message: '经验值不能为空', trigger: 'blur' }
          ],
          signTime: [
            { required: true, message: '签到时间不能为空', trigger: 'blur' }
          ],
          remark: [
            { required: true, message: '备注不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/generator/clastudentsign/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 200) {
                this.dataForm.taskId = data.claStudentSign.taskId
                this.dataForm.studentId = data.claStudentSign.studentId
                this.dataForm.courseId = data.claStudentSign.courseId
                this.dataForm.type = data.claStudentSign.type
                this.dataForm.longitude = data.claStudentSign.longitude
                this.dataForm.latitude = data.claStudentSign.latitude
                this.dataForm.experience = data.claStudentSign.experience
                this.dataForm.signTime = data.claStudentSign.signTime
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
              url: this.$http.adornUrl(`/generator/clastudentsign/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'taskId': this.dataForm.taskId,
                'studentId': this.dataForm.studentId,
                'courseId': this.dataForm.courseId,
                'type': this.dataForm.type,
                'longitude': this.dataForm.longitude,
                'latitude': this.dataForm.latitude,
                'experience': this.dataForm.experience,
                'signTime': this.dataForm.signTime,
                'remark': this.dataForm.remark
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
