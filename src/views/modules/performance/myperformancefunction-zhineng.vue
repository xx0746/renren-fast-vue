<template>
  <el-dialog
    title="评分"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户ID" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="用户ID" :disabled="true"></el-input>
    </el-form-item>
      <el-form-item label="用户" prop="userId">
        <el-input v-model="dataForm.userName" placeholder="用户" :disabled="true"></el-input>
      </el-form-item>

    <el-form-item label="工作业绩" prop="workload">
      <el-input v-model="dataForm.workload" placeholder="工作业绩"></el-input>
    </el-form-item>
    <el-form-item label="履职能力" prop="duty">
      <el-input v-model="dataForm.duty" placeholder="履职能力"></el-input>
    </el-form-item>
    <el-form-item label="工作纪律" prop="discipline">
      <el-input v-model="dataForm.discipline" placeholder="工作纪律"></el-input>
    </el-form-item>
    <el-form-item label="工作态度" prop="attitude">
      <el-input v-model="dataForm.attitude" placeholder="工作态度"></el-input>
    </el-form-item>

    <el-form-item label="绩效扣减" prop="deduction">
      <el-input v-model="dataForm.deduction" placeholder="绩效扣减"></el-input>
    </el-form-item>
      <el-form-item label="总分" prop="scorecount">
        <el-input v-model="dataForm.scorecount" placeholder="总分" :disabled="true"></el-input>
      </el-form-item>
      <el-form-item label="创建时间" prop="createTime">
        <el-input v-model="dataForm.createTime" placeholder="创建时间" :disabled="true"></el-input>
      </el-form-item>
      <el-form-item label="状态" prop="status">
        <el-input v-model="dataForm.status" placeholder="状态" :disabled="true"></el-input>
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
          duty: '',
          discipline: '',
          attitude: '',
          scorecount: '',
          deduction: ''
        },
        dataRule: {
          workload: [
            { required: true, message: '工作业绩不能为空', trigger: 'blur' }
          ],
          duty: [
            { required: true, message: '履职能力不能为空', trigger: 'blur' }
          ],
          discipline: [
            { required: true, message: '工作纪律不能为空', trigger: 'blur' }
          ],
          attitude: [
            { required: true, message: '工作态度不能为空', trigger: 'blur' }
          ],
          scorecount: [
            { required: true, message: '总分不能为空', trigger: 'blur' }
          ],
          deduction: [
            { required: true, message: '绩效扣减不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/sys/performancefunction/infobyid/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data.performanceFunction && data.code === 0) {
                this.dataForm.userId = data.performanceFunction.userId
                this.dataForm.createTime = data.performanceFunction.createTime
                this.dataForm.status = data.performanceFunction.status
                this.dataForm.workload = data.performanceFunction.workload
                this.dataForm.duty = data.performanceFunction.duty
                this.dataForm.discipline = data.performanceFunction.discipline
                this.dataForm.attitude = data.performanceFunction.attitude
                this.dataForm.scorecount = data.performanceFunction.scorecount
                this.dataForm.deduction = data.performanceFunction.deduction
                this.dataForm.userName = data.performanceFunction.userName
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.dataForm.scorecount = parseInt(this.dataForm.workload) + parseInt(this.dataForm.duty) + parseInt(this.dataForm.discipline) +
          parseInt(this.dataForm.attitude)-parseInt(this.dataForm.deduction)+'';
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/sys/performancefunction/${!this.dataForm.id ? 'savemy' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'createTime': this.dataForm.createTime,
                'status': this.dataForm.status,
                'workload': this.dataForm.workload,
                'duty': this.dataForm.duty,
                'discipline': this.dataForm.discipline,
                'attitude': this.dataForm.attitude,
                'scorecount': this.dataForm.scorecount,
                'deduction': this.dataForm.deduction
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
