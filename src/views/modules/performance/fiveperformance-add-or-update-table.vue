<template>
  <el-dialog
    :title="'评分'"
    :visible.sync="visible">
        <el-table
          :data="tableData"
          style="width: 100%">
          <el-table-column
            prop="id"
            label="序号"
            width="100">
          </el-table-column>
          <el-table-column
            prop="userName"
            label="姓名"
            width="100">
          </el-table-column>
          <el-table-column label="考核指标" align="center">
            <el-table-column label="工作量（30%）">
              <el-table-column label="工作量">
                <el-table-column
                  prop="workload"
                  label="30%"
                  width="120">
                </el-table-column>
              </el-table-column>
            </el-table-column>
            <el-table-column label="承担科研项目评价（30%）" align="center">
                <el-table-column label="先进性与创新性">
                  <el-table-column
                    prop="advanced"
                    label="10%"
                    width="120">
                  </el-table-column>
                </el-table-column>
              <el-table-column label="调查研究">
                <el-table-column
                  prop="research"
                  label="5%"
                  width="120">
                </el-table-column>
              </el-table-column>
              <el-table-column label="进度管控">
                <el-table-column
                  prop="process"
                  label="5%"
                  width="120">
                </el-table-column>
              </el-table-column>
              <el-table-column label="成果价值">
                <el-table-column
                  prop="achievement"
                  label="10%"
                  width="120">
                </el-table-column>
              </el-table-column>

            </el-table-column>
            <el-table-column label="工作能力及行为（20%）" align="center">
                <el-table-column label="岗位知识与技能">
                  <el-table-column
                    prop="evaluation"
                    label="4%"
                    width="120">
                  </el-table-column>
                </el-table-column>
              <el-table-column label="沟通协作能力">
                <el-table-column
                  prop="cooperation"
                  label="4%"
                  width="120">
                </el-table-column>
              </el-table-column>
              <el-table-column label="学习创新能力">
                <el-table-column
                  prop="learning"
                  label="4%"
                  width="120">
                </el-table-column>
              </el-table-column>
              <el-table-column label="工作责任心">
                <el-table-column
                  prop="responsibility"
                  label="4%"
                  width="120">
                </el-table-column>
              </el-table-column>
              <el-table-column label="工作纪律">
                <el-table-column
                  prop="discipline"
                  label="4%"
                  width="120">
                </el-table-column>
              </el-table-column>
            </el-table-column>
          </el-table-column>
          <el-table-column
            prop="examine"
            label="考核结果"
            width="100">
          </el-table-column>
          <el-table-column
            prop="coefficient"
            label="月度绩效（元）"
            width="100">
          </el-table-column>
        </el-table>
  </el-dialog>

</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          userId: '',
          userName: '',
          createTime: '',
          status: '',
          workload: '',
          advanced: '',
          research: '',
          process: '',
          achievement: '',
          evaluation: '',
          knowledge: '',
          cooperation: '',
          learning: '',
          responsibility: '',
          discipline: '',
          behavior: '',
          examine: '',
          rankage: '',
          coefficient: '',
          createUserId: '',
          checkUserId: ''
        },
        tableData: [{}],
        dataRule: {
          userId: [
            { required: true, message: '用户ID不能为空', trigger: 'blur' }
          ],
          workload: [
            { required: true, message: '工作量不能为空', trigger: 'blur' }
          ],
          process: [
            { required: true, message: '进度管控不能为空', trigger: 'blur' }
          ],
          achievement: [
            { required: true, message: '成果价值不能为空', trigger: 'blur' }
          ],
          evaluation: [
            { required: true, message: '承担科研项目评价不能为空', trigger: 'blur' }
          ],
          knowledge: [
            { required: true, message: '岗位知识与技能不能为空', trigger: 'blur' }
          ],
          cooperation: [
            { required: true, message: '沟通协作能力不能为空', trigger: 'blur' }
          ],
          learning: [
            { required: true, message: '学习创新能力不能为空', trigger: 'blur' }
          ],
          responsibility: [
            { required: true, message: '工作责任心不能为空', trigger: 'blur' }
          ],
          discipline: [
            { required: true, message: '工作纪律不能为空', trigger: 'blur' }
          ],
          behavior: [
            { required: true, message: '工作能力及行为不能为空', trigger: 'blur' }
          ],
          examine: [
            { required: true, message: '考核结果不能为空', trigger: 'blur' }
          ],
          coefficient: [
            { required: true, message: '绩效系数不能为空', trigger: 'blur' }
          ]

        }
      }
    },
    methods: {
      init (id) {
        var arr = this
        this.dataForm.userId = id
        this.visible = true
        if (this.dataForm.userId) {
          this.$http({
            url: this.$http.adornUrl(`/sys/performance/info/${this.dataForm.userId}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({data}) => {
            if (data.performance && data.code === 0) {
              arr.tableData = []
              arr.tableData.push(data.performance)
            }
          })
        }
      },
      // 表单提交
      dataFormSubmit () {
        this.dataForm.evaluation = parseInt(this.dataForm.advanced ? this.dataForm.advanced : '0') + parseInt(this.dataForm.research ? this.dataForm.research : '0') + parseInt(this.dataForm.process ? this.dataForm.process : '0') + parseInt(this.dataForm.achievement ? this.dataForm.achievement : '0') + '' ;
        this.dataForm.behavior = parseInt(this.dataForm.knowledge) + parseInt(this.dataForm.cooperation) + parseInt(this.dataForm.learning) + parseInt(this.dataForm.responsibility)+parseInt(this.dataForm.discipline)+'';
        this.dataForm.examine = parseInt(this.dataForm.evaluation ? this.dataForm.evaluation : '0') + parseInt(this.dataForm.behavior) + parseInt(this.dataForm.workload) +'';
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/sys/performance/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'createTime': this.dataForm.createTime,
                'status': this.dataForm.status,
                'workload': this.dataForm.workload,
                'advanced': this.dataForm.advanced,
                'research': this.dataForm.research,
                'process': this.dataForm.process,
                'achievement': this.dataForm.achievement,
                'evaluation': this.dataForm.evaluation,
                'knowledge': this.dataForm.knowledge,
                'cooperation': this.dataForm.cooperation,
                'learning': this.dataForm.learning,
                'responsibility': this.dataForm.responsibility,
                'discipline': this.dataForm.discipline,
                'behavior': this.dataForm.behavior,
                'examine': this.dataForm.examine,
                'range': this.dataForm.range,
                'coefficient': this.dataForm.coefficient,
                'createUserId': this.dataForm.createUserId,
                'checkUserId': this.dataForm.checkUserId
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
