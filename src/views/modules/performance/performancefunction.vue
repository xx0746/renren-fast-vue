<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="参数名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button  type="primary" @click="exportExcel()">导出模板</el-button>
        <el-button type="primary" @click="uploadHandle()">导入</el-button>
<!--        <el-button v-if="isAuth('sys:performancefunction:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>-->
<!--        <el-button v-if="isAuth('sys:performancefunction:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>-->
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
<!--      <el-table-column-->
<!--        prop="id"-->
<!--        header-align="center"-->
<!--        align="center"-->
<!--        label="">-->
<!--      </el-table-column>-->
<!--      <el-table-column-->
<!--        prop="userId"-->
<!--        header-align="center"-->
<!--        align="center"-->
<!--        label="用户ID">-->
<!--      </el-table-column>-->
<!--      <el-table-column-->
<!--        prop="createTime"-->
<!--        header-align="center"-->
<!--        align="center"-->
<!--        label="创建时间">-->
<!--      </el-table-column>-->
<!--      <el-table-column-->
<!--        prop="status"-->
<!--        header-align="center"-->
<!--        align="center"-->
<!--        label="状态">-->
<!--      </el-table-column>-->
<!--      <el-table-column-->
<!--        prop="workload"-->
<!--        header-align="center"-->
<!--        align="center"-->
<!--        label="工作业绩">-->
<!--      </el-table-column>-->
<!--      <el-table-column-->
<!--        prop="duty"-->
<!--        header-align="center"-->
<!--        align="center"-->
<!--        label="履职能力">-->
<!--      </el-table-column>-->
<!--      <el-table-column-->
<!--        prop="discipline"-->
<!--        header-align="center"-->
<!--        align="center"-->
<!--        label="工作纪律">-->
<!--      </el-table-column>-->
<!--      <el-table-column-->
<!--        prop="attitude"-->
<!--        header-align="center"-->
<!--        align="center"-->
<!--        label="工作态度">-->
<!--      </el-table-column>-->
<!--      <el-table-column-->
<!--        prop="scorecount"-->
<!--        header-align="center"-->
<!--        align="center"-->
<!--        label="总分">-->
<!--      </el-table-column>-->
<!--      <el-table-column-->
<!--        prop="deduction"-->
<!--        header-align="center"-->
<!--        align="center"-->
<!--        label="绩效扣减">-->
<!--      </el-table-column>-->
      <el-table-column
        prop="userId"
        header-align="center"
        align="center"
        width="80"
        label="ID">
      </el-table-column>
      <el-table-column
        prop="username"
        header-align="center"
        align="center"
        label="用户名">
      </el-table-column>
      <el-table-column
        prop="email"
        header-align="center"
        align="center"
        label="邮箱">
      </el-table-column>
      <el-table-column
        prop="mobile"
        header-align="center"
        align="center"
        label="手机号">
      </el-table-column>
      <el-table-column
        prop="roleName"
        header-align="center"
        align="center"
        label="部门">
      </el-table-column>
      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        label="状态">
        <template slot-scope="scope">
          <el-tag v-if="scope.row.status === 0" size="small" type="danger">禁用</el-tag>
          <el-tag v-else size="small">正常</el-tag>
        </template>
      </el-table-column>
      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
        width="180"
        label="创建时间">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button  type="text" size="small" @click="addOrUpdateHandle(scope.row.userId)">评分</el-button>
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
    <upload v-if="uploadVisible"  @refreshDataList="getDataList" ref="upload" ></upload>

  </div>
</template>

<script>
  import AddOrUpdate from './performancefunction-add-or-update'
  import Upload from './performancefunction-upload'

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
        uploadVisible: false
      }
    },
    components: {
      AddOrUpdate,
      Upload
    },
    activated () {
      this.getDataList()
    },
    methods: {
      uploadHandle () {
        this.uploadVisible = true
        this.$nextTick(() => {
          this.$refs.upload.init()
        })
      },
      exportExcel () {
        window.location.href = this.$http.adornUrl('/sys/performancefunction/export?token=' + this.$cookie.get('token'))
      },
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/sys/user/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'key': this.dataForm.key
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
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
            url: this.$http.adornUrl('/sys/performancefunction/delete'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({data}) => {
            if (data && data.code === 0) {
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
