<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="condition.name" placeholder="参数名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-date-picker
          v-model="condition.createTime"
          type="month"
          value-format="yyyy-MM"
          placeholder="选择月">
        </el-date-picker>
      </el-form-item>
      <el-form-item>
        <el-button @click="pageListWithCondition()">查询设计</el-button>
        <el-button  type="primary" @click="exportExcel()">导出</el-button>
        <el-button type="primary" @click="uploadHandle()">导入</el-button>
        <el-button type="primary" @click="uploadFile()">文件上传</el-button>
        <el-button type="primary" @click="downloadFile()">文件下载</el-button>
        <el-switch
          v-model="audit.status == 1"
          active-color="#13ce66"
          inactive-color="#ff4949"
          style="margin-left: 40px"
          @change="updateAudit()"
        >
        </el-switch>

<!--        <el-switch-->
<!--          v-if="audit.status ==0 "-->
<!--          v-model="audit.status == 1"-->
<!--          active-text="未审核"-->
<!--          active-color="#13ce66"-->
<!--          inactive-color="#ff4949"-->
<!--          style="margin-left: 40px"-->
<!--          @change="updateAudit()"-->
<!--          >-->
<!--        </el-switch>-->


        <span style="margin-right: 5px" v-if="audit.status == 1">已审核</span>
        <span style="margin-right: 5px" v-if="audit.status == 0">未审核</span>
      </el-form-item>
    </el-form>
    <el-table
      :data="page.records"
      border
      :default-sort = "{prop: 'sordId', order: 'ascending'}"

      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>
      <el-table-column
        prop="sortId"
        header-align="center"
        align="center"
        sortable
        label="序号">
      </el-table-column>
      <el-table-column
        prop="name"
        header-align="center"
        align="center"
        label="姓名">
      </el-table-column>
      <el-table-column
        prop="level"
        header-align="center"
        align="center"
        label="层级">
      </el-table-column>

      <el-table-column
        prop="wordLoad"
        header-align="center"
        align="center"
        label="工作量">
      </el-table-column>
      <el-table-column
        prop="knowledgeSkills"
        header-align="center"
        align="center"
        label="岗位知识与技能">
      </el-table-column>
      <el-table-column
        prop="communicationCollaboration"
        header-align="center"
        align="center"
        label="沟通协作能力">
      </el-table-column>
      <el-table-column
        prop="learningInnovation"
        header-align="center"
        align="center"
        label="学习创新能力">
      </el-table-column>
      <el-table-column
        prop="jobResponsibility"
        header-align="center"
        align="center"
        label="工作责任心">
      </el-table-column>
      <el-table-column
        prop="workingDiscipline"
        header-align="center"
        align="center"
        label="工作纪律">
      </el-table-column>
      <el-table-column
        prop="evaluationResult"
        header-align="center"
        align="center"
        sortable
        :sort-orders="['ascending','descending']"
        label="考核结果">
      </el-table-column>
      <el-table-column
        prop="evaluationLevel"
        header-align="center"
        align="center"
        label="考核等级">
      </el-table-column>
      <el-table-column
        prop="performance"
        header-align="center"
        align="center"
        label="月度绩效(元)">
      </el-table-column>
      <el-table-column
        prop="remark"
        header-align="center"
        align="center"
        label="备注">
      </el-table-column>
      <!--      <el-table-column-->
      <!--        fixed="right"-->
      <!--        header-align="center"-->
      <!--        align="center"-->
      <!--        width="150"-->
      <!--        label="操作">-->
      <!--        <template slot-scope="scope">-->

      <!--          <el-button  type="text" size="small" v-if="isAuth('sys:performance:shenhe')"  @click="shenhe(scope.row.id)">审核</el-button>-->
      <!--          <el-button  type="text" size="small" v-if="isAuth('sys:performance:rlshenhe')"  @click="shenhe(scope.row.id)">人力审核</el-button>-->
      <!--          <el-button  type="text" size="small" v-if="isAuth('sys:performance:yuanshenhe')"  @click="shenhe(scope.row.id)">院审核</el-button>-->
      <!--          <el-button  type="text" size="small" v-if="!isAuth('sys:performance:shenhe') && !isAuth('sys:performance:rlshenhe') && !isAuth('sys:performance:yuanshenhe')"  @click="addOrUpdateHandle(scope.row.id)">修改</el-button>-->
      <!--          <el-button type="text" size="small" v-if="!isAuth('sys:performance:shenhe') && !isAuth('sys:performance:rlshenhe') && !isAuth('sys:performance:yuanshenhe')"  @click="deleteHandle(scope.row.id)">删除</el-button>-->
      <!--        </template>-->
      <!--      </el-table-column>-->
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
  import AddOrUpdate from './myperformance-add-or-update'
  import Upload from './performance-upload'
  export default {
    data () {
      return {
        audit: {
          // centID的中心 0:设计中心 1:金融中心 2:战略中心 3:技术中心 4:情报中心 5:质检中心
          centerId: 0,
          status: 0
        },
        dataForm: {
          key: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        uploadVisible: false,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        page: {},
        condition: {
          name: '',
          createTime: ''
        }
      }
    },
    components: {
      AddOrUpdate,
      Upload
    },
    activated () {
      this.getDataList()
    },
    created () {
      let date = new Date()
      let year = date.getFullYear()
      let month = date.getMonth() + 1
      if (month < 10) {
        month = '0' + month
      }
      this.condition.createTime = year + '-' + month
      console.log(this.condition.createTime)
      this.pageListWithCondition()
      this.getStatus()
    },
    methods: {
      getStatus () {
        this.$http({
          url: this.$http.adornUrl('/audit/getStatus'),
          method: 'get',
          params: this.$http.adornParams({
            'centerId': this.audit.centerId
          })
        }).then(({data}) => {
          this.audit.status = data.status
        })
      },
      updateAudit () {
        this.$http({
          url: this.$http.adornUrl('/audit/updateAudit'),
          method: 'post',
          params: this.$http.adornParams({
            'centerId': this.audit.centerId,
            'status': this.audit.status
          })
        }).then(({data}) => {
          if (data.code === 403) {
            this.$message.info('权限不足')
          } else {
            this.getStatus()
          }
        })
      },
      uploadHandle () {
        this.uploadVisible = true
        this.$nextTick(() => {
          this.$refs.upload.init('/department/design/uploadExcel?createTime=' + this.condition.createTime)
        })
      },
      uploadFile () {
        this.uploadVisible = true
        this.$nextTick(() => {
          this.$refs.upload.init('/file/uploadFile?fileName=科研中心员工绩效考核评分表-设计')
        })
      },
      downloadFile () {
        window.location.href = this.$http.adornUrl('/file/downloadFile?fileName=科研中心员工绩效考核评分表-设计' + '&token=' + this.$cookie.get('token'))
      },
      exportExcel () {
        // window.location.href = this.$http.adornUrl('/sys/performance/export?date=' + this.dataForm.date + '&token=' + this.$cookie.get('token'))
        window.location.href = this.$http.adornUrl('/department/design/exportExcel?name=' + this.condition.name + '&createTime=' + this.condition.createTime + '&token=' + this.$cookie.get('token'))
      },
      pageListWithCondition () {
        this.$http({
          url: this.$http.adornUrl('/department/design/pageListWithCondition'),
          method: 'post',
          params: this.$http.adornParams({
            'name': this.condition.name,
            'createTime': this.condition.createTime,
            'current': this.pageIndex,
            'pageSize': this.pageSize
          })
        }).then(({data}) => {
          this.page = data.page
          this.totalPage = data.page.total
          // if (data && data.code === 0) {
          //   this.dataList = data.page.list
          //   this.totalPage = data.page.totalCount
          // } else {
          //   this.dataList = []
          //   this.totalPage = 0
          // }
          //this.dataListLoading = true
        })
      },
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

      // 每页数
      sizeChangeHandle (val) {
        this.pageSize = val
        this.pageIndex = 1
        this.pageListWithCondition()
      },
      // 当前页
      currentChangeHandle (val) {
        this.pageIndex = val
        this.pageListWithCondition()
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
