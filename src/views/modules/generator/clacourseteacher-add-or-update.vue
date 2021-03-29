<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="任课教师ID" prop="teacherId">
      <el-input v-model="dataForm.teacherId" placeholder="任课教师ID"></el-input>
    </el-form-item>
    <el-form-item label="发起签到次数" prop="sign">
      <el-input v-model="dataForm.sign" placeholder="发起签到次数"></el-input>
    </el-form-item>
    <el-form-item label="满经验值" prop="fullExp">
      <el-input v-model="dataForm.fullExp" placeholder="满经验值"></el-input>
    </el-form-item>
    <el-form-item label="创建者" prop="createBy">
      <el-input v-model="dataForm.createBy" placeholder="创建者"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
    </el-form-item>
    <el-form-item label="更新者" prop="updateBy">
      <el-input v-model="dataForm.updateBy" placeholder="更新者"></el-input>
    </el-form-item>
    <el-form-item label="更新时间" prop="updateTime">
      <el-input v-model="dataForm.updateTime" placeholder="更新时间"></el-input>
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
          courseId: 0,
          teacherId: '',
          sign: '',
          fullExp: '',
          createBy: '',
          createTime: '',
          updateBy: '',
          updateTime: '',
          remark: ''
        },
        dataRule: {
          teacherId: [
            { required: true, message: '任课教师ID不能为空', trigger: 'blur' }
          ],
          sign: [
            { required: true, message: '发起签到次数不能为空', trigger: 'blur' }
          ],
          fullExp: [
            { required: true, message: '满经验值不能为空', trigger: 'blur' }
          ],
          createBy: [
            { required: true, message: '创建者不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ],
          updateBy: [
            { required: true, message: '更新者不能为空', trigger: 'blur' }
          ],
          updateTime: [
            { required: true, message: '更新时间不能为空', trigger: 'blur' }
          ],
          remark: [
            { required: true, message: '备注不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.courseId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.courseId) {
            this.$http({
              url: this.$http.adornUrl(`/generator/clacourseteacher/info/${this.dataForm.courseId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.teacherId = data.claCourseTeacher.teacherId
                this.dataForm.sign = data.claCourseTeacher.sign
                this.dataForm.fullExp = data.claCourseTeacher.fullExp
                this.dataForm.createBy = data.claCourseTeacher.createBy
                this.dataForm.createTime = data.claCourseTeacher.createTime
                this.dataForm.updateBy = data.claCourseTeacher.updateBy
                this.dataForm.updateTime = data.claCourseTeacher.updateTime
                this.dataForm.remark = data.claCourseTeacher.remark
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
              url: this.$http.adornUrl(`/generator/clacourseteacher/${!this.dataForm.courseId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'courseId': this.dataForm.courseId || undefined,
                'teacherId': this.dataForm.teacherId,
                'sign': this.dataForm.sign,
                'fullExp': this.dataForm.fullExp,
                'createBy': this.dataForm.createBy,
                'createTime': this.dataForm.createTime,
                'updateBy': this.dataForm.updateBy,
                'updateTime': this.dataForm.updateTime,
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
