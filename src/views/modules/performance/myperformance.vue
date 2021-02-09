<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="参数名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('sys:performance:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('sys:performance:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>
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
        label="ID">
      </el-table-column>
      <el-table-column
        prop="userName"
        header-align="center"
        align="center"
        v-if="isAuth('sys:performance:shenhe')"
        label="用户">
      </el-table-column>
      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
        label="创建时间">
      </el-table-column>

      <el-table-column
        prop="workload"
        header-align="center"
        align="center"
        label="工作量">
      </el-table-column>
      <el-table-column
        prop="advanced"
        header-align="center"
        align="center"
        label="先进性与创新性">
      </el-table-column>
      <el-table-column
        prop="research"
        header-align="center"
        align="center"
        label="调查研究">
      </el-table-column>
      <el-table-column
        prop="process"
        header-align="center"
        align="center"
        label="进度管控">
      </el-table-column>
      <el-table-column
        prop="achievement"
        header-align="center"
        align="center"
        label="成果价值">
      </el-table-column>
      <el-table-column
        prop="evaluation"
        header-align="center"
        align="center"
        label="承担科研项目评价">
      </el-table-column>
      <el-table-column
        prop="knowledge"
        header-align="center"
        align="center"
        label="岗位知识与技能">
      </el-table-column>
      <el-table-column
        prop="cooperation"
        header-align="center"
        align="center"
        label="沟通协作能力">
      </el-table-column>
      <el-table-column
        prop="learning"
        header-align="center"
        align="center"
        label="学习创新能力">
      </el-table-column>
      <el-table-column
        prop="responsibility"
        header-align="center"
        align="center"
        label="工作责任心">
      </el-table-column>
      <el-table-column
        prop="discipline"
        header-align="center"
        align="center"
        label="工作纪律">
      </el-table-column>
      <el-table-column
        prop="behavior"
        header-align="center"
        align="center"
        label="工作能力及行为">
      </el-table-column>
      <el-table-column
        prop="examine"
        header-align="center"
        align="center"
        label="考核结果">
      </el-table-column>
      <el-table-column
        prop="rankage"
        header-align="center"
        align="center"
        label="绩效考核得分排名">
      </el-table-column>
      <el-table-column
        prop="coefficient"
        header-align="center"
        align="center"
        label="绩效系数">
      </el-table-column>
      <el-table-column
        prop="createUserId"
        header-align="center"
        align="center"
        label="录入者ID">
      </el-table-column>
      <el-table-column
        prop="checkUserId"
        header-align="center"
        align="center"
        label="审核者ID">
      </el-table-column>
      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        :formatter="stateFormat"
        label="状态">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">

          <el-button  type="text" size="small" v-if="isAuth('sys:performance:shenhe')"  @click="shenhe(scope.row.id)">审核</el-button>
          <el-button  type="text" size="small" v-if="isAuth('sys:performance:rlshenhe')"  @click="shenhe(scope.row.id)">人力审核</el-button>
          <el-button  type="text" size="small" v-if="isAuth('sys:performance:yuanshenhe')"  @click="shenhe(scope.row.id)">院审核</el-button>
          <el-button  type="text" size="small" v-if="!isAuth('sys:performance:shenhe') && !isAuth('sys:performance:rlshenhe') && !isAuth('sys:performance:yuanshenhe')"  @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button type="text" size="small" v-if="!isAuth('sys:performance:shenhe') && !isAuth('sys:performance:rlshenhe') && !isAuth('sys:performance:yuanshenhe')"  @click="deleteHandle(scope.row.id)">删除</el-button>
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
import AddOrUpdate from './myperformance-add-or-update'
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
      addOrUpdateVisible: false
    }
  },
  components: {
    AddOrUpdate
  },
  activated () {
    this.getDataList()
  },
  methods: {
    shenhe (id) {
      this.addOrUpdateVisible = true
      this.$nextTick(() => {
        this.$refs.addOrUpdate.init(id)
      })
    },
    stateFormat (row,colum){
      if (row.status === 1) {
        return '待审核'
      } else if (row.status === 2){
        return '驳回'
      }else if (row.status === 3){
        return '审核通过'
      }
    },
    // 获取数据列表
    getDataList () {
      this.dataListLoading = true
      this.$http({
        url: this.$http.adornUrl('/sys/performance/mylist'),
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
          url: this.$http.adornUrl('/sys/performance/delete'),
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
