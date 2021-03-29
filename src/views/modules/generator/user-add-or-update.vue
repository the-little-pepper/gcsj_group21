<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="学校系ID" prop="uniacadaId">
      <el-input v-model="dataForm.uniacadaId" placeholder="学校系ID"></el-input>
    </el-form-item>
    <el-form-item label="用户账号" prop="userName">
      <el-input v-model="dataForm.userName" placeholder="用户账号"></el-input>
    </el-form-item>
    <el-form-item label="用户昵称" prop="nickName">
      <el-input v-model="dataForm.nickName" placeholder="用户昵称"></el-input>
    </el-form-item>
    <el-form-item label="学号" prop="studentNum">
      <el-input v-model="dataForm.studentNum" placeholder="学号"></el-input>
    </el-form-item>
    <el-form-item label="用户类型（00系统用户）" prop="userType">
      <el-input v-model="dataForm.userType" placeholder="用户类型（00系统用户）"></el-input>
    </el-form-item>
    <el-form-item label="用户邮箱" prop="email">
      <el-input v-model="dataForm.email" placeholder="用户邮箱"></el-input>
    </el-form-item>
    <el-form-item label="手机号码" prop="phonenumber">
      <el-input v-model="dataForm.phonenumber" placeholder="手机号码"></el-input>
    </el-form-item>
    <el-form-item label="头像地址" prop="avatar">
      <el-input v-model="dataForm.avatar" placeholder="头像地址"></el-input>
    </el-form-item>
    <el-form-item label="密码" prop="password">
      <el-input v-model="dataForm.password" placeholder="密码"></el-input>
    </el-form-item>
    <el-form-item label="帐号状态（0正常 1停用）" prop="status">
      <el-input v-model="dataForm.status" placeholder="帐号状态（0正常 1停用）"></el-input>
    </el-form-item>
    <el-form-item label="删除标志（0代表存在 2代表删除）" prop="delFlag">
      <el-input v-model="dataForm.delFlag" placeholder="删除标志（0代表存在 2代表删除）"></el-input>
    </el-form-item>
    <el-form-item label="最后登陆IP" prop="loginIp">
      <el-input v-model="dataForm.loginIp" placeholder="最后登陆IP"></el-input>
    </el-form-item>
    <el-form-item label="最后登陆时间" prop="loginDate">
      <el-input v-model="dataForm.loginDate" placeholder="最后登陆时间"></el-input>
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
          userId: 0,
          uniacadaId: '',
          userName: '',
          nickName: '',
          studentNum: '',
          userType: '',
          email: '',
          phonenumber: '',
          avatar: '',
          password: '',
          status: '',
          delFlag: '',
          loginIp: '',
          loginDate: '',
          createBy: '',
          createTime: '',
          updateBy: '',
          updateTime: '',
          remark: ''
        },
        dataRule: {
          uniacadaId: [
            { required: true, message: '学校系ID不能为空', trigger: 'blur' }
          ],
          userName: [
            { required: true, message: '用户账号不能为空', trigger: 'blur' }
          ],
          nickName: [
            { required: true, message: '用户昵称不能为空', trigger: 'blur' }
          ],
          studentNum: [
            { required: true, message: '学号不能为空', trigger: 'blur' }
          ],
          userType: [
            { required: true, message: '用户类型（00系统用户）不能为空', trigger: 'blur' }
          ],
          email: [
            { required: true, message: '用户邮箱不能为空', trigger: 'blur' }
          ],
          phonenumber: [
            { required: true, message: '手机号码不能为空', trigger: 'blur' }
          ],
          avatar: [
            { required: true, message: '头像地址不能为空', trigger: 'blur' }
          ],
          password: [
            { required: true, message: '密码不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '帐号状态（0正常 1停用）不能为空', trigger: 'blur' }
          ],
          delFlag: [
            { required: true, message: '删除标志（0代表存在 2代表删除）不能为空', trigger: 'blur' }
          ],
          loginIp: [
            { required: true, message: '最后登陆IP不能为空', trigger: 'blur' }
          ],
          loginDate: [
            { required: true, message: '最后登陆时间不能为空', trigger: 'blur' }
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
        this.dataForm.userId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.userId) {
            this.$http({
              url: this.$http.adornUrl(`/generator/user/info/${this.dataForm.userId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.uniacadaId = data.user.uniacadaId
                this.dataForm.userName = data.user.userName
                this.dataForm.nickName = data.user.nickName
                this.dataForm.studentNum = data.user.studentNum
                this.dataForm.userType = data.user.userType
                this.dataForm.email = data.user.email
                this.dataForm.phonenumber = data.user.phonenumber
                this.dataForm.avatar = data.user.avatar
                this.dataForm.password = data.user.password
                this.dataForm.status = data.user.status
                this.dataForm.delFlag = data.user.delFlag
                this.dataForm.loginIp = data.user.loginIp
                this.dataForm.loginDate = data.user.loginDate
                this.dataForm.createBy = data.user.createBy
                this.dataForm.createTime = data.user.createTime
                this.dataForm.updateBy = data.user.updateBy
                this.dataForm.updateTime = data.user.updateTime
                this.dataForm.remark = data.user.remark
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
              url: this.$http.adornUrl(`/generator/user/${!this.dataForm.userId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'userId': this.dataForm.userId || undefined,
                'uniacadaId': this.dataForm.uniacadaId,
                'userName': this.dataForm.userName,
                'nickName': this.dataForm.nickName,
                'studentNum': this.dataForm.studentNum,
                'userType': this.dataForm.userType,
                'email': this.dataForm.email,
                'phonenumber': this.dataForm.phonenumber,
                'avatar': this.dataForm.avatar,
                'password': this.dataForm.password,
                'status': this.dataForm.status,
                'delFlag': this.dataForm.delFlag,
                'loginIp': this.dataForm.loginIp,
                'loginDate': this.dataForm.loginDate,
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
