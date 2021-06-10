<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="名称" prop="name">
      <el-input v-model="dataForm.name" placeholder="字典名称"></el-input>
    </el-form-item>
    <el-form-item label="关键字" prop="type">
      <el-input v-model="dataForm.type" placeholder="字典关键字"></el-input>
    </el-form-item>
    <!--
    <el-form-item label="字典码" prop="code">
      <el-input v-model="dataForm.code" placeholder="字典码"></el-input>
    </el-form-item>
    <el-form-item label="字典值" prop="value">
      <el-input v-model="dataForm.value" placeholder="字典值" required></el-input>
    </el-form-item>
    <el-form-item label="排序" prop="orderNum">
      <el-input v-model="dataForm.orderNum" placeholder="排序"></el-input>
    </el-form-item>
    -->
    <el-form-item label="备注" prop="remark">
      <el-input v-model="dataForm.remark" placeholder="备注"></el-input>
    </el-form-item>
    <!--
    <el-form-item label="删除标记 -1:已删除" prop="delFlag">
      <el-input v-model="dataForm.delFlag" placeholder="删除标记 -1:已删除"></el-input>
    </el-form-item>
    -->
    <el-form-item label="状态" prop="status">
      <el-radio-group  v-model="dataForm.status">
        <el-radio class="radio" :label="1" :key="1">正常</el-radio>
        <el-radio class="radio" :label="0" :key="0">禁用</el-radio>
      </el-radio-group>
    </el-form-item>
    </el-form>

    <el-table
      :data="dataList"
      ref="dragTable"
      highlight-current-row
      row-key="id"
      border
      v-loading="dataListLoading"
      style="width: 100%;">
      <el-table-column
        prop="showid"
        header-align="center"
        align="center"
        label="序号">
      </el-table-column>
      <el-table-column
        prop="code"
        header-align="center"
        align="center"
        label="值">
      </el-table-column>
      <el-table-column
        prop="value"
        header-align="center"
        align="center"
        label="文本">
      </el-table-column>
      <el-table-column
        prop="delFlag"
        header-align="center"
        align="center"
        :formatter="formatDefault"
        label="默认值">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="editHandle(scope.row.id)">编辑</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

    <el-row>
      <el-button @click="showsub()" type="primary" icon="el-icon-upload" style="width:100%"><icon-svg name="zhonghe"></icon-svg> 添加数据项</el-button>
    </el-row>

    <el-form v-if="subvisible" :inline="true" style="margin-top: 20px" :model="subForm" :rules="subRule" ref="subForm" @keyup.enter.native="subFormSubmit()" label-width="80px">
      <el-form-item label="值" prop="code">
        <el-input v-model="subForm.code" placeholder="值"></el-input>
      </el-form-item>
      <el-form-item label="文本" prop="value">
        <el-input v-model="subForm.value" placeholder="文本"></el-input>
      </el-form-item>
      <el-form-item label="默认值" prop="delFlag">
      <el-radio-group  v-model="subForm.delFlag">
        <el-radio class="radio" :label="1" :key="1">是</el-radio>
        <el-radio class="radio" :label="0" :key="0">否</el-radio>
      </el-radio-group>
    </el-form-item>
      <el-form-item>
        <template>
          <el-button type="text" size="small" @click="subFormSubmit()">保存</el-button>
          <el-button type="text" size="small" @click="cancel()">取消</el-button>
        </template>
      </el-form-item>
      <!-- <el-row>
        <el-col :span="3" :offset="4">
          <el-input v-model="subForm.code" placeholder="值"></el-input>
        </el-col>
        <el-col :span="8" :offset="1">
          <el-input v-model="subForm.value" placeholder="文本"></el-input>
        </el-col>
        <el-col :span="3" :offset="1">
          <el-input v-model="subForm.delFlag" placeholder="默认值"></el-input>
        </el-col>
        <el-col :span="3" :offset="1">
          <template>
            <el-button type="text" size="small">保存</el-button>
            <el-button type="text" size="small" @click="cancel()">取消</el-button>
          </template>
        </el-col>
      </el-row> -->
    </el-form>

    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false; subvisible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  import Sortable from 'sortablejs'

  export default {
    data () {
      const paramKeyValidator = async (rule, value, callback) => {
        if (!value) {
          return callback(new Error('字典码不能为空'))
        }
        // 获取数据字典
        const {data} = await this.$http({
          url: this.$http.adornUrl('/sysdict/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': 2000,
            'key': this.dataForm.key
          })
        })
        let isRepeat = false
        for (let item of data.page.list) {
          if (value === item.code && this.dataForm.type === item.type) {
            if ((this.subForm.id && this.subForm.id !== item.id) || !this.subForm.id) {
              isRepeat = true
              break
            }
          }
        }

        if (isRepeat) {
          return callback(new Error('字典码重复'))
        } else {
          return callback()
        }
      }
      return {
        visible: false,
        dataListLoading: false,
        subvisible: false,
        dataList: [],
        dataForm: {
          id: 0,
          name: '',
          type: '',
          code: '',
          value: '',
          orderNum: '0',
          remark: '无',
          delFlag: '0',
          status: 1
        },
        subForm: {
          id: 0,
          name: '',
          type: '',
          code: '',
          value: '',
          orderNum: '0',
          remark: '无',
          delFlag: 0,
          status: 1
        },
        subRule: {
          code: [
            { required: true, validator: paramKeyValidator, trigger: 'blur' }
          ],
          value: [
            { required: true, message: '字典值不能为空', trigger: 'blur' }
          ],
          delFlag: [
            { required: true, message: '删除标记 -1:已删除不能为空', trigger: 'blur' }
          ]
        },
        dataRule: {
          name: [
            { required: true, message: '字典名称不能为空', trigger: 'blur' }
          ],
          type: [
            { required: true, message: '字典类型不能为空', trigger: 'blur' }
          ],
          code: [
            { required: true, validator: paramKeyValidator, trigger: 'blur' }
          ],
          value: [
            { required: true, message: '字典值不能为空', trigger: 'blur' }
          ],
          orderNum: [
            { required: false, trigger: 'blur' }
          ],
          remark: [
            { trigger: 'blur' }
          ],
          status: [
            { required: true, message: '0 禁用 1正常不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      formatDefault: function (row, column) {
        return row.delFlag === 1 ? '是' : '否'
      },
      initsub () {
        this.dataListLoading = true
        this.dataList = []
        // 获取数据字典
        this.$http({
          url: this.$http.adornUrl('/sysdict/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': 1,
            'limit': 2000
          })
        }).then(({data}) => {
          let i = 1
          for (let item of data.page.list) {
            if (this.dataForm.type === item.type && item.delFlag !== 88) {
              item.showid = i++
              this.dataList.push(item)
            }
          }
          this.dataListLoading = false
        })
      },
      init (id) {
        this.dataForm.id = id || 0

        this.visible = true
        this.$nextTick(() => {
          // 拖拽排序
          const el = this.$refs['dragTable'].$el.querySelectorAll('.el-table__body-wrapper > table > tbody')[0]
          this.sortable = Sortable.create(el, {
            ghostClass: 'sortable-ghost',
            setData: function (dataTransfer) {
              dataTransfer.setData('Text', '')
            },
            onEnd: evt => {
              const targetRow = this.dataList.splice(evt.oldIndex, 1)[0]
              this.dataList.splice(evt.newIndex, 0, targetRow)

              for (let i = 0; i < this.dataList.length; i++) {
                this.$http({
                  url: this.$http.adornUrl(`/sysdict/update`),
                  method: 'post',
                  data: this.$http.adornData({
                    'id': this.dataList[i].id,
                    'orderNum': this.dataList.length - i
                  })
                }).then(({data}) => {
                  if (data && data.code === 200) {
                    console.log('ok')
                  } else {
                    this.$message.error(data.msg)
                  }
                })
              }
            }
          })

          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/sysdict/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 200) {
                this.dataForm.name = data.sysDict.name
                this.dataForm.type = data.sysDict.type
                this.dataForm.code = data.sysDict.code
                this.dataForm.value = data.sysDict.value
                this.dataForm.orderNum = data.sysDict.orderNum
                this.dataForm.remark = data.sysDict.remark
                this.dataForm.delFlag = data.sysDict.delFlag
                this.dataForm.status = data.sysDict.status

                this.initsub()
              }
            })
          } else {
            this.dataList = []
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/sysdict/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'name': this.dataForm.name,
                'type': this.dataForm.type,
                'code': this.dataForm.code,
                'value': this.dataForm.value,
                'orderNum': this.dataForm.orderNum,
                'remark': this.dataForm.remark,
                'delFlag': 88,
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
      },
      // 删除
      deleteHandle (id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/sysdict/delete'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({data}) => {
            if (data && data.code === 200) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.cancel()
                  this.initsub()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        })
      },
      // 取消
      cancel () {
        this.subForm = {
          id: 0,
          name: '',
          type: '',
          code: '',
          value: '',
          orderNum: '0',
          remark: '无',
          delFlag: 0,
          status: 1
        }
        this.subvisible = false
      },
      // 子表单提交
      subFormSubmit () {
        this.$refs['subForm'].validate(async (valid) => {
          if (valid) {
            if (parseInt(this.subForm.delFlag) === 1) {
              for (let i = 0; i < this.dataList.length; i++) {
                await this.$http({
                  url: this.$http.adornUrl(`/sysdict/update`),
                  method: 'post',
                  data: this.$http.adornData({
                    'id': this.dataList[i].id,
                    'delFlag': 0
                  })
                })
              }
            }
            this.$http({
              url: this.$http.adornUrl(`/sysdict/${!this.subForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.subForm.id || undefined,
                'name': this.dataForm.name,
                'type': this.dataForm.type,
                'code': this.subForm.code,
                'value': this.subForm.value,
                'orderNum': this.subForm.orderNum,
                'remark': this.dataForm.remark,
                'delFlag': this.subForm.delFlag,
                'status': 1
              })
            }).then(({data}) => {
              if (data && data.code === 200) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.cancel()
                    this.initsub()
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      },
      // 显示添加
      showsub () {
        if (this.subvisible) {
          this.$message({
            message: '已存在记录未保存，请先保存',
            type: 'info',
            duration: 1000
          })
        }

        let maxid = -1
        for (let item of this.dataList) {
          maxid = maxid >= parseInt(item.code) ? maxid : parseInt(item.code)
        }
        this.subForm.code = '' + (maxid + 1)

        this.subvisible = true
      },
      // 编辑子字典
      editHandle (id) {
        this.subForm.id = id || 0
        this.$http({
          url: this.$http.adornUrl(`/sysdict/info/${this.subForm.id}`),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          if (data && data.code === 200) {
            this.subForm.code = data.sysDict.code
            this.subForm.value = data.sysDict.value
            this.subForm.orderNum = data.sysDict.orderNum
            this.subForm.delFlag = data.sysDict.delFlag

            this.subvisible = true
          }
        })
      }
    }
  }
</script>
