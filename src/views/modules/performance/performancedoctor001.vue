<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="参数名" clearable></el-input>
      </el-form-item>
      <el-form-item v-if="isAuth('sys:performancedoctor:shenhe')">
        <el-select v-model="dataForm.status" placeholder="请选择" @change="statusChange">
          <el-option
            v-for="item in options"
            :key="item.value"
            :label="item.label"
            :value="item.value"
            >
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <el-button v-if="isAuth('sys:performancedoctor:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button>
        <el-button v-if="isAuth('sys:performancedoctor:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>
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
        prop="name"
        header-align="center"
        align="center"
        label="用户名">
      </el-table-column>
      <el-table-column
        prop="userName"
        header-align="center"
        align="center"
        v-if="isAuth('sys:performancedoctor:shenhe')"
        label="用户名">
      </el-table-column>
      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
        label="创建时间">
      </el-table-column>
      <el-table-column
        prop="status"
        header-align="center"
        align="center"
        :formatter="stateFormat"
        label="状态">
      </el-table-column>
      <el-table-column
        prop="report"
        header-align="center"
        align="center"
        label="专报成果">
      </el-table-column>
      <el-table-column
        prop="achievement"
        header-align="center"
        align="center"
        label="科研课题成果">
      </el-table-column>
      <el-table-column
        prop="paper"
        header-align="center"
        align="center"
        label="发表论文情况">
      </el-table-column>
      <el-table-column
        prop="classes"
        header-align="center"
        align="center"
        label="院内外授课情况">
      </el-table-column>
      <el-table-column
        prop="workstu"
        header-align="center"
        align="center"
        label="中心科研工作情况">
      </el-table-column>
      <el-table-column
        prop="centerSug"
        header-align="center"
        align="center"
        label="所在中心考核意见">
      </el-table-column>
      <el-table-column
        prop="scientificSug"
        header-align="center"
        align="center"
        label="科研管理部考核得分">
      </el-table-column>
      <el-table-column
        prop="examineSug"
        header-align="center"
        align="center"
        label="人力资源部审核意见">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" v-if="isAuth('sys:performancedoctor:update')" @click="addOrUpdateHandle(scope.row.id)">修改</el-button>
          <el-button type="text" v-if="isAuth('sys:performancedoctor:shenhe')" size="small" @click="shenhe(scope.row.id)">审核</el-button>
          <el-button type="text" v-if="isAuth('sys:performancedoctor:shenhe2')" size="small" @click="shenhe(scope.row.id)">审核</el-button>
          <el-button type="text" v-if="isAuth('sys:performancedoctor:rlshenhe')" size="small" @click="shenhe(scope.row.id)">人力审核</el-button>
          <el-button type="text" v-if="isAuth('sys:performancedoctor:yuanshenhe')" size="small" @click="shenhe(scope.row.id)">院审核</el-button>
          <el-button type="text" v-if="isAuth('sys:performancedoctor:delete')" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
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
    <Shenhe v-if="shenHe" ref="addOrUpdate" @refreshDataList="getDataList"></Shenhe>
  </div>
</template>

<script>
  import AddOrUpdate from './performancedoctor-add-or-update'
  import Shenhe from './performancedoctor-shenhe'
  export default {
    data () {
      return {
        dataForm: {
          key: '',
          status: ''
        },
        options: [{
          value: '1',
          label: '待审核'
        }, {
          value: '3',
          label: '已审核',
        }
        ],
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        shenHe: false
      }
    },
    components: {
      AddOrUpdate,
      Shenhe
    },
    activated () {
      this.getDataList()
    },
    methods: {
      statusChange(val) {
        this.getDataList()
      },
      stateFormat (row,colum){
        if (row.status === 1) {
          return '待审核'
        } else if (row.status === 2){
          return '驳回'
        }else if (row.status === 3){
          return '中心审核通过'
        }else if (row.status === 5){
          return '科研审核通过'
        }else if (row.status === 7){
          return '院审核通过'
        }else if (row.status === 9){
          return '人力审核通过'
        }
      },
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true;
        this.$http({
          url: this.$http.adornUrl('/sys/performancedoctor/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'key': this.dataForm.key,
            'status': this.dataForm.status
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
      shenhe (id) {
        this.shenHe = true
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
            url: this.$http.adornUrl('/sys/performancedoctor/delete'),
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
