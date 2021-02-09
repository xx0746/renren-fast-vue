<template>
  <el-dialog
    :title="'评分'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户id" prop="userId" >
      <el-input v-model="dataForm.userId" placeholder="用户ID" :disabled="true"></el-input>
    </el-form-item>
      <el-form-item label="用户" prop="userName" >
        <el-input v-model="dataForm.userName" placeholder="用户" :disabled="true"></el-input>
      </el-form-item>
    <el-form-item label="创建时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="创建时间" :disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="状态" prop="status">
      <el-input v-model="dataForm.status" placeholder="状态" :disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="工作量" prop="workload">
      <el-input v-model="dataForm.workload" placeholder="工作量"></el-input>
    </el-form-item>
    <el-form-item label="先进性与创新性" prop="advanced" v-if="isAuth('sys:performance:zlshenhe')">
      <el-input v-model="dataForm.advanced" placeholder="先进性与创新性"></el-input>
    </el-form-item>
    <el-form-item label="调查研究" prop="research">
      <el-input v-model="dataForm.research" placeholder="调查研究"></el-input>
    </el-form-item>
    <el-form-item label="进度管控" prop="process">
      <el-input v-model="dataForm.process" placeholder="进度管控"></el-input>
    </el-form-item>
    <el-form-item label="成果价值" prop="achievement">
      <el-input v-model="dataForm.achievement" placeholder="成果价值"></el-input>
    </el-form-item>
    <el-form-item label="承担科研项目评价" prop="evaluation">
      <el-input v-model="dataForm.evaluation" placeholder="" :disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="岗位知识与技能" prop="knowledge">
      <el-input v-model="dataForm.knowledge" placeholder="岗位知识与技能"></el-input>
    </el-form-item>
    <el-form-item label="沟通协作能力" prop="cooperation">
      <el-input v-model="dataForm.cooperation" placeholder="沟通协作能力"></el-input>
    </el-form-item>
    <el-form-item label="学习创新能力" prop="learning">
      <el-input v-model="dataForm.learning" placeholder="学习创新能力"></el-input>
    </el-form-item>
    <el-form-item label="工作责任心" prop="responsibility">
      <el-input v-model="dataForm.responsibility" placeholder="工作责任心"></el-input>
    </el-form-item>
    <el-form-item label="工作纪律" prop="discipline">
      <el-input v-model="dataForm.discipline" placeholder="工作纪律"></el-input>
    </el-form-item>
    <el-form-item label="工作能力及行为" prop="behavior">
      <el-input v-model="dataForm.behavior" placeholder="工作能力及行为" :disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="考核结果" prop="examine">
      <el-input v-model="dataForm.examine" placeholder="考核结果" :disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="绩效考核得分排名" prop="range">
      <el-input v-model="dataForm.rankage" placeholder="绩效考核得分排名" :disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="绩效系数" prop="coefficient">
      <el-input v-model="dataForm.coefficient" placeholder="绩效系数"></el-input>
    </el-form-item>
    <el-form-item label="录入者ID" prop="createUserId">
      <el-input v-model="dataForm.createUserId" placeholder="录入者ID" :disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="审核者ID" prop="checkUserId">
      <el-input v-model="dataForm.checkUserId" placeholder="审核者ID" :disabled="true"></el-input>
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
        dataRule: {
          userId: [
            { required: true, message: '用户ID不能为空', trigger: 'blur' }
          ],
          workload: [
            { required: true, message: '工作量不能为空', trigger: 'blur' }
          ],
          advanced: [
            { required: true, message: '先进性与创新性不能为空', trigger: 'blur' }
          ],
          research: [
            { required: true, message: '调查研究不能为空', trigger: 'blur' }
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
        this.dataForm.userId = id
        this.visible = true
        this.$nextTick(() => {
          if (this.dataForm.userId) {
            this.$http({
              url: this.$http.adornUrl(`/sys/performance/info/${this.dataForm.userId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data.performance && data.code === 0) {
                this.dataForm.id  = data.performance.id
                this.dataForm.userId = data.performance.userId
                this.dataForm.userName = data.performance.userName
                this.dataForm.createTime = data.performance.createTime
                this.dataForm.status = data.performance.status
                this.dataForm.workload = data.performance.workload
                this.dataForm.advanced = data.performance.advanced
                this.dataForm.research = data.performance.research
                this.dataForm.process = data.performance.process
                this.dataForm.achievement = data.performance.achievement
                this.dataForm.evaluation = data.performance.evaluation
                this.dataForm.knowledge = data.performance.knowledge
                this.dataForm.cooperation = data.performance.cooperation
                this.dataForm.learning = data.performance.learning
                this.dataForm.responsibility = data.performance.responsibility
                this.dataForm.discipline = data.performance.discipline
                this.dataForm.behavior = data.performance.behavior
                this.dataForm.examine = data.performance.examine
                this.dataForm.range = data.performance.range
                this.dataForm.coefficient = data.performance.coefficient
                this.dataForm.createUserId = data.performance.createUserId
                this.dataForm.checkUserId = data.performance.checkUserId
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.dataForm.evaluation = parseInt(this.dataForm.advanced) + parseInt(this.dataForm.research) + parseInt(this.dataForm.process) + parseInt(this.dataForm.achievement)+'';
        this.dataForm.behavior = parseInt(this.dataForm.knowledge) + parseInt(this.dataForm.cooperation) + parseInt(this.dataForm.learning) + parseInt(this.dataForm.responsibility)+parseInt(this.dataForm.discipline)+'';
        this.dataForm.examine = parseInt(this.dataForm.evaluation) + parseInt(this.dataForm.behavior) + parseInt(this.dataForm.workload) +'';
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
