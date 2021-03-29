<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="课程名称" prop="courseName">
      <el-input v-model="dataForm.courseName" placeholder="课程名称"></el-input>
    </el-form-item>
    <el-form-item label="班课号" prop="courseNum">
      <el-input v-model="dataForm.courseNum" placeholder="班课号"></el-input>
    </el-form-item>
    <el-form-item label="班级名称" prop="className">
      <el-input v-model="dataForm.className" placeholder="班级名称"></el-input>
    </el-form-item>
    <el-form-item label="班课封面" prop="coursePage">
      <el-input v-model="dataForm.coursePage" placeholder="班课封面"></el-input>
    </el-form-item>
    <el-form-item label="学期" prop="semester">
      <el-input v-model="dataForm.semester" placeholder="学期"></el-input>
    </el-form-item>
    <el-form-item label="学校课表班课" prop="curriculum">
      <el-input v-model="dataForm.curriculum" placeholder="学校课表班课"></el-input>
    </el-form-item>
    <el-form-item label="云教材" prop="textbook">
      <el-input v-model="dataForm.textbook" placeholder="云教材"></el-input>
    </el-form-item>
    <el-form-item label="学校院系ID" prop="uniacadaId">
      <el-input v-model="dataForm.uniacadaId" placeholder="学校院系ID"></el-input>
    </el-form-item>
    <el-form-item label="学习要求" prop="studyRequirement">
      <el-input v-model="dataForm.studyRequirement" placeholder="学习要求"></el-input>
    </el-form-item>
    <el-form-item label="教学进度" prop="lectureProgress">
      <el-input v-model="dataForm.lectureProgress" placeholder="教学进度"></el-input>
    </el-form-item>
    <el-form-item label="考试安排" prop="examArrangement">
      <el-input v-model="dataForm.examArrangement" placeholder="考试安排"></el-input>
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
          courseName: '',
          courseNum: '',
          className: '',
          coursePage: '',
          semester: '',
          curriculum: '',
          textbook: '',
          uniacadaId: '',
          studyRequirement: '',
          lectureProgress: '',
          examArrangement: '',
          createBy: '',
          createTime: '',
          updateBy: '',
          updateTime: '',
          remark: ''
        },
        dataRule: {
          courseName: [
            { required: true, message: '课程名称不能为空', trigger: 'blur' }
          ],
          courseNum: [
            { required: true, message: '班课号不能为空', trigger: 'blur' }
          ],
          className: [
            { required: true, message: '班级名称不能为空', trigger: 'blur' }
          ],
          coursePage: [
            { required: true, message: '班课封面不能为空', trigger: 'blur' }
          ],
          semester: [
            { required: true, message: '学期不能为空', trigger: 'blur' }
          ],
          curriculum: [
            { required: true, message: '学校课表班课不能为空', trigger: 'blur' }
          ],
          textbook: [
            { required: true, message: '云教材不能为空', trigger: 'blur' }
          ],
          uniacadaId: [
            { required: true, message: '学校院系ID不能为空', trigger: 'blur' }
          ],
          studyRequirement: [
            { required: true, message: '学习要求不能为空', trigger: 'blur' }
          ],
          lectureProgress: [
            { required: true, message: '教学进度不能为空', trigger: 'blur' }
          ],
          examArrangement: [
            { required: true, message: '考试安排不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/generator/clacourse/info/${this.dataForm.courseId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.courseName = data.claCourse.courseName
                this.dataForm.courseNum = data.claCourse.courseNum
                this.dataForm.className = data.claCourse.className
                this.dataForm.coursePage = data.claCourse.coursePage
                this.dataForm.semester = data.claCourse.semester
                this.dataForm.curriculum = data.claCourse.curriculum
                this.dataForm.textbook = data.claCourse.textbook
                this.dataForm.uniacadaId = data.claCourse.uniacadaId
                this.dataForm.studyRequirement = data.claCourse.studyRequirement
                this.dataForm.lectureProgress = data.claCourse.lectureProgress
                this.dataForm.examArrangement = data.claCourse.examArrangement
                this.dataForm.createBy = data.claCourse.createBy
                this.dataForm.createTime = data.claCourse.createTime
                this.dataForm.updateBy = data.claCourse.updateBy
                this.dataForm.updateTime = data.claCourse.updateTime
                this.dataForm.remark = data.claCourse.remark
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
              url: this.$http.adornUrl(`/generator/clacourse/${!this.dataForm.courseId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'courseId': this.dataForm.courseId || undefined,
                'courseName': this.dataForm.courseName,
                'courseNum': this.dataForm.courseNum,
                'className': this.dataForm.className,
                'coursePage': this.dataForm.coursePage,
                'semester': this.dataForm.semester,
                'curriculum': this.dataForm.curriculum,
                'textbook': this.dataForm.textbook,
                'uniacadaId': this.dataForm.uniacadaId,
                'studyRequirement': this.dataForm.studyRequirement,
                'lectureProgress': this.dataForm.lectureProgress,
                'examArrangement': this.dataForm.examArrangement,
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
