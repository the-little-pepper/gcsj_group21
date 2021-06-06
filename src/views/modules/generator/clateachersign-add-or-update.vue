<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="课程id" prop="courseId">
      <el-input v-model="dataForm.courseId" placeholder="课程id"></el-input>
    </el-form-item>
    <el-form-item label="教师id" prop="teacherId">
      <el-input v-model="dataForm.teacherId" placeholder="教师id"></el-input>
    </el-form-item>
    <el-form-item label="签到类型" prop="type">
      <el-input v-model="dataForm.type" placeholder="签到类型"></el-input>
    </el-form-item>
    <el-form-item label="签到开始时间" prop="startTime">
      <el-input v-model="dataForm.startTime" placeholder="签到开始时间"></el-input>
    </el-form-item>
    <el-form-item label="签到结束时间" prop="stopTime">
      <el-input v-model="dataForm.stopTime" placeholder="签到结束时间"></el-input>
    </el-form-item>
    <el-form-item label="纬度" prop="latitude">
      <el-input v-model="dataForm.latitude" placeholder="纬度"></el-input>
    </el-form-item>
    <el-form-item label="经度" prop="longitude">
      <el-input v-model="dataForm.longitude" placeholder="经度"></el-input>
    </el-form-item>
    <el-form-item label="一键签到结束标志 0是结束 1还在签到中" prop="finished">
      <el-input v-model="dataForm.finished" placeholder="一键签到结束标志 0是结束 1还在签到中"></el-input>
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
          courseId: '',
          teacherId: '',
          type: '',
          startTime: '',
          stopTime: '',
          latitude: '',
          longitude: '',
          finished: ''
        },
        dataRule: {
          courseId: [
            { required: true, message: '课程id不能为空', trigger: 'blur' }
          ],
          teacherId: [
            { required: true, message: '教师id不能为空', trigger: 'blur' }
          ],
          type: [
            { required: true, message: '签到类型不能为空', trigger: 'blur' }
          ],
          startTime: [
            { required: true, message: '签到开始时间不能为空', trigger: 'blur' }
          ],
          stopTime: [
            { required: true, message: '签到结束时间不能为空', trigger: 'blur' }
          ],
          latitude: [
            { required: true, message: '纬度不能为空', trigger: 'blur' }
          ],
          longitude: [
            { required: true, message: '经度不能为空', trigger: 'blur' }
          ],
          finished: [
            { required: true, message: '一键签到结束标志 0是结束 1还在签到中不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/generator/clateachersign/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 200) {
                this.dataForm.courseId = data.claTeacherSign.courseId
                this.dataForm.teacherId = data.claTeacherSign.teacherId
                this.dataForm.type = data.claTeacherSign.type
                this.dataForm.startTime = data.claTeacherSign.startTime
                this.dataForm.stopTime = data.claTeacherSign.stopTime
                this.dataForm.latitude = data.claTeacherSign.latitude
                this.dataForm.longitude = data.claTeacherSign.longitude
                this.dataForm.finished = data.claTeacherSign.finished
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
              url: this.$http.adornUrl(`/generator/clateachersign/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'courseId': this.dataForm.courseId,
                'teacherId': this.dataForm.teacherId,
                'type': this.dataForm.type,
                'startTime': this.dataForm.startTime,
                'stopTime': this.dataForm.stopTime,
                'latitude': this.dataForm.latitude,
                'longitude': this.dataForm.longitude,
                'finished': this.dataForm.finished
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
