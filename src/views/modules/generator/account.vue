<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="参数名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('generator:account:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('generator:account:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>
      <el-table-column
        prop="id"
        header-align="center"
        align="center"
        label="账号表id">
      </el-table-column>
      <el-table-column
        prop="userId"
        header-align="center"
        align="center"
        label="用户表id">
      </el-table-column>
      <el-table-column
        prop="type"
        header-align="center"
        align="center"
        :formatter="formatType"
        label="账号类型">
      </el-table-column>
      <el-table-column
        prop="userName"
        header-align="center"
        align="center"
        label="用户名包括邮箱手机号等">
      </el-table-column>
      <el-table-column
        prop="password"
        header-align="center"
        align="center"
        label="密码">
      </el-table-column>
      <el-table-column
        prop="status"
        header-align="center"
        :formatter="formatBan"
        align="center"
        label="禁用标志">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>

<script>
  import AddOrUpdate from './account-add-or-update'
  export default {
    data () {
      return {
        dataForm: {
          key: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        sysdict: {}
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getDataList()
    },
    methods: {
      formatType: function (row, column) {
        return this.sysdict['account_type'][row.type]
      },
      formatBan: function (row, column) {
        return row.status === '1' ? '正常' : '禁用'
      },
      // 获取数据列表
      async getDataList () {
        this.dataListLoading = true
        // 获得数据字典
        const {data} = await this.$http({
          url: this.$http.adornUrl('/sysdict/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': 200,
            'key': this.dataForm.key
          })
        })
        for (let item of data.page.list) {
          console.log(item.code, item.type, item.value)
          if (!this.sysdict[item.type]) {
            this.sysdict[item.type] = {}
          }
          this.sysdict[item.type][item.code] = item.value
        }

        this.$http({
          url: this.$http.adornUrl('/generator/account/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'key': this.dataForm.key
          })
        }).then(({data}) => {
          if (data && data.code === 200) {
            this.dataList = data.page.list
            this.totalPage = data.page.totalCount
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      // 每页数
      sizeChangeHandle (val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle (val) {
        this.pageIndex = val
        this.getDataList()
      },
      // 多选
      selectionChangeHandle (val) {
        this.dataListSelections = val
      },
      // 新增 / 修改
      addOrUpdateHandle (id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      // 删除
      deleteHandle (id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/generator/account/delete'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({data}) => {
            if (data && data.code === 200) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        })
      }
    }
  }
</script>
