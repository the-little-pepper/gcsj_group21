<template>
  <el-dialog
    :title="!dataForm.userId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户名" prop="userName">
      <el-input v-model="dataForm.userName" placeholder="用户名"></el-input>
    </el-form-item>
    <el-form-item label="工号" prop="eduNum">
      <el-input v-model="dataForm.eduNum" placeholder="学号"></el-input>
    </el-form-item>
    <!-- <el-form-item label="用户类型" prop="userType">
      <el-radio-group  v-model="dataForm.userType">
        <el-radio v-for="(type, i) in sysdict['user_type']" class="radio" :label="i" :key="i">{{type}}</el-radio>
      </el-radio-group>
    </el-form-item> -->
    <el-form-item label="性别" prop="sex">
      <el-radio-group  v-model="dataForm.sex">
        <el-radio v-for="(sex, i) in sysdict['sex']" class="radio" :label="i" :key="i">{{sex}}</el-radio>
      </el-radio-group>
    </el-form-item>
    <el-form-item label="用户昵称" prop="nickName">
      <el-input v-model="dataForm.nickName" placeholder="用户昵称"></el-input>
    </el-form-item>
    <el-form-item label="学校院系" prop="uniacadaId">
      <el-popover
          ref="menuListPopover"
          placement="bottom-start"
          trigger="click">
          <el-tree
            :data="menuList"
            :props="menuListTreeProps"
            node-key="menuId"
            ref="menuListTree"
            @current-change="menuListTreeCurrentChangeHandle"
            :default-expand-all="true"
            :highlight-current="true"
            :expand-on-click-node="false">
          </el-tree>
      </el-popover>
      <el-input v-model="dataForm.parentName" v-popover:menuListPopover :readonly="true" placeholder="点击选择学校" class="menu-list__input"></el-input>
      
    </el-form-item>
    <!--
    <el-form-item label="头像地址" prop="avatar">
      <el-input v-model="dataForm.avatar" placeholder="头像地址"></el-input>
    </el-form-item>
    -->
    <!-- <el-form-item label="最后登陆时间" prop="loginDate">
      <el-input v-model="dataForm.loginDate" placeholder="最后登陆时间" :disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="创建时间" :disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="创建者" prop="createBy">
      <el-input v-model="dataForm.createBy" placeholder="创建者" :disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="更新时间" prop="updateTime">
      <el-input v-model="dataForm.updateTime" placeholder="更新时间" :disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="更新者" prop="updateBy">
      <el-input v-model="dataForm.updateBy" placeholder="更新者" :disabled="true"></el-input>
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
  import { treeDataTranslate } from '@/utils'
  export default {
    data () {
      const validateSchool = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('学校院系不能为空'))
        } else {
          if (this.dataForm.parentId === 0) {
            callback(new Error('请选择院系'))
          }
        }
        return callback()
      }
      return {
        schools: {},
        visible: false,
        dataForm: {
          userId: 0,
          userName: '',
          eduNum: '',
          userType: '',
          sex: '',
          nickName: '',
          uniacadaId: '',
          avatar: '',
          loginDate: '',
          createTime: '',
          createBy: '',
          updateTime: '',
          updateBy: '',
          remark: '',
          parentId: '',
          parentName: ''
        },
        sysdict: {},
        dataRule: {
          userName: [
            { required: true, message: '用户名不能为空', trigger: 'blur' }
          ],
          eduNum: [
            { required: true, message: '工号不能为空', trigger: 'blur' }
          ],
          userType: [
            { required: true, message: '用户类型不能为空', trigger: 'blur' }
          ],
          sex: [
            { required: true, message: '性别不能为空', trigger: 'blur' }
          ],
          nickName: [
            { required: true, message: '用户昵称不能为空', trigger: 'blur' }
          ],
          uniacadaId: [
            {validator: validateSchool, trigger: 'change'}
          ],
          avatar: [
            { required: true, message: '头像地址不能为空', trigger: 'blur' }
          ],
          loginDate: [
            { required: true, message: '最后登陆时间不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ],
          createBy: [
            { required: true, message: '创建者不能为空', trigger: 'blur' }
          ],
          updateTime: [
            { required: true, message: '更新时间不能为空', trigger: 'blur' }
          ],
          updateBy: [
            { required: true, message: '更新者不能为空', trigger: 'blur' }
          ],
          remark: [
            { required: true, message: '备注不能为空', trigger: 'blur' }
          ]
        },
        menuList: [],
        menuListTreeProps: {
          label: 'name',
          children: 'children'
        }
      }
    },
    methods: {
      getSchools () {
        this.$http({
          url: this.$http.adornUrl('/generator/school/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': 0,
            'limit': 200
          })
        }).then(({data}) => {
          this.menuList = treeDataTranslate(data, 'menuId')
          for (let item of data) {
            this.schools[item.menuId] = item.parentName + ' ' + item.name
          }
          this.dataForm.parentName = this.dataForm.uniacadaId === '' ? '' : this.schools[this.dataForm.uniacadaId]
        })
      },
      async init (id) {
        // 获得数据字典
        let {data} = await this.$http({
          url: this.$http.adornUrl('/sysdict/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': 200,
            'key': this.dataForm.key
          })
        })
        for (let item of data.page.list) {
          if (!this.sysdict[item.type]) {
            this.sysdict[item.type] = {}
          }
          if (item.delFlag === 88) {
            continue
          }
          this.sysdict[item.type][item.code] = item.value
        }

        this.getSchools()

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
              if (data && data.code === 200) {
                this.dataForm.userName = data.user.userName
                this.dataForm.eduNum = data.user.eduNum
                this.dataForm.userType = data.user.userType
                this.dataForm.sex = data.user.sex
                this.dataForm.nickName = data.user.nickName
                this.dataForm.uniacadaId = data.user.uniacadaId
                this.dataForm.avatar = data.user.avatar
                this.dataForm.loginDate = data.user.loginDate
                this.dataForm.createTime = data.user.createTime
                this.dataForm.createBy = data.user.createBy
                this.dataForm.updateTime = data.user.updateTime
                this.dataForm.updateBy = data.user.updateBy
                this.dataForm.remark = data.user.remark

              }
            })
          }
        })
      },
      // 菜单树选中
      menuListTreeCurrentChangeHandle (data, node) {
        this.dataForm.uniacadaId = data.menuId
        this.dataForm.parentName = data.parentId === 0 ? data.name : data.parentName + ' ' + data.name
        this.dataForm.parentId = data.parentId
        this.$refs.menuListPopover.showPopper = false
      },
      // 菜单树设置当前选中节点
      menuListTreeSetCurrentNode () {
        this.$refs.menuListTree.setCurrentKey(this.dataForm.parentId)
        this.dataForm.parentName = (this.$refs.menuListTree.getCurrentNode() || {})['name']
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            var dateFormat = require('dateformat')
            this.$http({
              url: this.$http.adornUrl(`/generator/user/${!this.dataForm.userId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'userId': this.dataForm.userId || undefined,
                'userName': this.dataForm.userName,
                'eduNum': this.dataForm.eduNum,
                'userType': 1,
                'sex': this.dataForm.sex,
                'nickName': this.dataForm.nickName,
                'uniacadaId': this.dataForm.uniacadaId,
                'avatar': this.dataForm.avatar,
                'loginDate': this.dataForm.loginDate,
                'createTime': this.dataForm.createTime,
                'createBy': this.dataForm.createBy,
                'updateTime': dateFormat(new Date(), 'yyyy-mm-dd HH:MM:ss'),
                'updateBy': this.dataForm.updateBy,
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
