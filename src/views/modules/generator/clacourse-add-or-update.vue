<template>
  <el-dialog
    :title="!dataForm.courseId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="课程名称" prop="courseName">
      <el-input v-model="dataForm.courseName" placeholder="课程名称" :disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="班课号" prop="courseNum">
      <el-input v-model="dataForm.courseNum" placeholder="班课号" :disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="班级名称" prop="className">
      <el-input v-model="dataForm.className" placeholder="班级名称" :disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="学期" prop="semester">
      <el-input v-model="dataForm.semester" placeholder="学期" :disabled="true"></el-input>
    </el-form-item>
    <!-- <el-form-item label="学校课表班课" prop="curriculum">
      <el-input v-model="dataForm.curriculum" placeholder="学校课表班课"></el-input>
    </el-form-item>
    <el-form-item label="云教材" prop="textbook">
      <el-input v-model="dataForm.textbook" placeholder="云教材"></el-input>
    </el-form-item> -->
    <el-form-item label="学校院系" prop="uniacadaId">
      <el-input v-model="schools[dataForm.uniacadaId]" placeholder="学校院系ID" :disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="学习要求" prop="studyRequirement">
      <el-input v-model="dataForm.studyRequirement" placeholder="学习要求"></el-input>
    </el-form-item>
    <el-form-item label="考试安排" prop="examArrangement">
      <el-input v-model="dataForm.examArrangement" placeholder="考试安排"></el-input>
    </el-form-item>
    <el-form-item label="状态" prop="status">
      <el-radio-group  v-model="dataForm.status">
        <el-radio v-for="(name, i) in statustypes" class="radio" :label="i" :key="i">{{name}}</el-radio>
      </el-radio-group>
    </el-form-item>
    <el-form-item label="教学进度" prop="overs">
      <el-radio-group  v-model="dataForm.overs">
        <el-radio v-for="(name, i) in overstypes" class="radio" :label="i" :key="i">{{name}}</el-radio>
      </el-radio-group>
    </el-form-item>
    <!-- <el-form-item label="创建者" prop="createBy">
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
    </el-form-item> -->
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
        statustypes: {1: '不能加入', 0: '允许加入'},
        overstypes: {1: '已结束', 0: '进行中'},
        schools: [],
        visible: false,
        dataForm: {
          courseId: 0,
          courseName: '',
          courseNum: '',
          className: '',
          status: '',
          semester: '',
          uniacadaId: '',
          studyRequirement: '',
          overs: '',
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
          status: [
            { required: true, message: '班课封面不能为空', trigger: 'blur' }
          ],
          semester: [
            { required: true, message: '学期不能为空', trigger: 'blur' }
          ],
          uniacadaId: [
            { required: true, message: '学校院系不能为空', trigger: 'change' }
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
      async init (id) {
        const {data} = await this.$http({
          url: this.$http.adornUrl('/generator/school/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': 0,
            'limit': 200
          })
        })
        for (let item of data) {
          this.schools[item.menuId] = item.parentName + ' ' + item.name
        }

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
              if (data && data.code === 200) {
                this.dataForm.courseName = data.claCourse.courseName
                this.dataForm.courseNum = data.claCourse.courseNum
                this.dataForm.className = data.claCourse.className
                this.dataForm.status = data.claCourse.status.toString()
                this.dataForm.semester = data.claCourse.semester
                this.dataForm.uniacadaId = data.claCourse.uniacadaId
                this.dataForm.studyRequirement = data.claCourse.studyRequirement
                this.dataForm.overs = data.claCourse.overs.toString()
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
            var dateFormat = require('dateformat')
            this.$http({
              url: this.$http.adornUrl(`/generator/clacourse/${!this.dataForm.courseId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'courseId': this.dataForm.courseId || undefined,
                'courseName': this.dataForm.courseName,
                'courseNum': this.dataForm.courseNum,
                'className': this.dataForm.className,
                'status': this.dataForm.status,
                'semester': this.dataForm.semester,
                'uniacadaId': this.dataForm.uniacadaId,
                'studyRequirement': this.dataForm.studyRequirement,
                'overs': this.dataForm.overs,
                'examArrangement': this.dataForm.examArrangement,
                'createBy': this.dataForm.createBy,
                'createTime': this.dataForm.createTime,
                'updateBy': this.dataForm.updateBy,
                'updateTime': dateFormat(new Date(), 'yyyy-mm-dd HH:MM:ss'),
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
