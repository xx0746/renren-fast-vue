<template>
  <el-dialog
    title="审核"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">


    <el-form-item label="专报成果" prop="report">
      <el-input v-model="dataForm.report" placeholder="专报成果" :disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="科研课题成果" prop="achievement">
      <el-input v-model="dataForm.achievement" placeholder="科研课题成果" :disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="发表论文情况" prop="paper">
      <el-input v-model="dataForm.paper" placeholder="发表论文情况" :disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="院内外授课情况" prop="classes">
      <el-input v-model="dataForm.classes" placeholder="院内外授课情况" :disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="中心科研工作情况" prop="workstu">
      <el-input v-model="dataForm.workstu" placeholder="中心科研工作情况" :disabled="true"></el-input>
    </el-form-item>
    <el-form-item label="所在中心考核意见" prop="centerSug" v-if="isAuth('sys:performancedoctor:shenhe')">
      <el-input v-model="dataForm.centerSug" placeholder="所在中心考核意见" :disabled="false"></el-input>
    </el-form-item>
      <el-form-item label="所在中心考核意见" prop="centerSug" v-else >
        <el-input v-model="dataForm.centerSug" placeholder="所在中心考核意见" :disabled="true"></el-input>
      </el-form-item>
    <el-form-item label="科研管理部考核得分" prop="scientificSug" v-if="isAuth('sys:performancedoctor:shenhe2')">
      <el-input v-model="dataForm.scientificSug" placeholder="科研管理部考核得分" :disabled="false"></el-input>
    </el-form-item>
      <el-form-item label="科研管理部考核得分" prop="scientificSug" v-else >
        <el-input v-model="dataForm.scientificSug" placeholder="科研管理部考核得分" :disabled="true"></el-input>
      </el-form-item>
    <el-form-item label="人力资源部审核意见" prop="examineSug" v-if="isAuth('sys:performancedoctor:rlshenhe') ">
      <el-input v-model="dataForm.examineSug" placeholder="人力资源部审核意见" :disabled="false"></el-input>
    </el-form-item>
      <el-form-item label="人力资源部审核意见" prop="examineSug" v-else  >
        <el-input v-model="dataForm.examineSug" placeholder="人力资源部审核意见" :disabled="true"></el-input>
      </el-form-item>
      <el-form-item label="状态" prop="status">
        <el-input v-model="dataForm.status" placeholder="状态" :disabled="true"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" v-if="isAuth('sys:performancedoctor:shenhe') " @click="dataFormSubmit()">审核通过</el-button>
      <el-button type="primary" v-if="isAuth('sys:performancedoctor:rlshenhe') " @click="rlSubmit()">人力审核通过</el-button>
      <el-button type="primary" v-if="isAuth('sys:performancedoctor:yuanshenhe') " @click="yuanSubmit()">院审核通过</el-button>
      <el-button type="primary" v-if="isAuth('sys:performancedoctor:shenhe2')" @click="kysub()">确定</el-button>
      <el-button type="danger" v-if="isAuth('sys:performancedoctor:shenhe')" @click="bohuiSubmit()">驳回</el-button>
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
          createTime: '',
          status: '',
          report: '',
          achievement: '',
          paper: '',
          classes: '',
          workstu: '',
          centerSug: '',
          scientificSug: '',
          examineSug: ''
        },
        dataRule: {

          report: [
            { required: true, message: '专报成果不能为空', trigger: 'blur' }
          ],
          achievement: [
            { required: true, message: '科研课题成果不能为空', trigger: 'blur' }
          ],
          paper: [
            { required: true, message: '发表论文情况不能为空', trigger: 'blur' }
          ],
          classes: [
            { required: true, message: '院内外授课情况不能为空', trigger: 'blur' }
          ],
          workstu: [
            { required: true, message: '中心科研工作情况不能为空', trigger: 'blur' }
          ],
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/sys/performancedoctor/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.performanceDoctor.userId
                this.dataForm.createTime = data.performanceDoctor.createTime
                this.dataForm.status = data.performanceDoctor.status
                this.dataForm.report = data.performanceDoctor.report
                this.dataForm.achievement = data.performanceDoctor.achievement
                this.dataForm.paper = data.performanceDoctor.paper
                this.dataForm.classes = data.performanceDoctor.classes
                this.dataForm.workstu = data.performanceDoctor.workstu
                this.dataForm.centerSug = data.performanceDoctor.centerSug
                this.dataForm.scientificSug = data.performanceDoctor.scientificSug
                this.dataForm.examineSug = data.performanceDoctor.examineSug
              }
            })
          }
        })
      },
      kysub () {
        this.dataForm.status = 5
        this.$http({
          url: this.$http.adornUrl(`/sys/performancedoctor/shenhe2`),
          method: 'post',
          data: this.$http.adornData(this.dataForm)
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
      },
      // 表单提交
      dataFormSubmit () {
        this.dataForm.status = 3
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/sys/performancedoctor/shenhe`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'createTime': this.dataForm.createTime,
                'status': this.dataForm.status,
                'report': this.dataForm.report,
                'achievement': this.dataForm.achievement,
                'paper': this.dataForm.paper,
                'classes': this.dataForm.classes,
                'workstu': this.dataForm.workstu,
                'centerSug': this.dataForm.centerSug,
                'scientificSug': this.dataForm.scientificSug,
                'examineSug': this.dataForm.examineSug
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
      },
      rlSubmit () {
        this.dataForm.status = 9
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/sys/performancedoctor/rlshenhe`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'createTime': this.dataForm.createTime,
                'status': this.dataForm.status,
                'report': this.dataForm.report,
                'achievement': this.dataForm.achievement,
                'paper': this.dataForm.paper,
                'classes': this.dataForm.classes,
                'workstu': this.dataForm.workstu,
                'centerSug': this.dataForm.centerSug,
                'scientificSug': this.dataForm.scientificSug,
                'examineSug': this.dataForm.examineSug
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
      },
      yuanSubmit () {
        this.dataForm.status = 7
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/sys/performancedoctor/yuanshenhe`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'createTime': this.dataForm.createTime,
                'status': this.dataForm.status,
                'report': this.dataForm.report,
                'achievement': this.dataForm.achievement,
                'paper': this.dataForm.paper,
                'classes': this.dataForm.classes,
                'workstu': this.dataForm.workstu,
                'centerSug': this.dataForm.centerSug,
                'scientificSug': this.dataForm.scientificSug,
                'examineSug': this.dataForm.examineSug
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
      },
      bohuiSubmit () {
        this.dataForm.status = 2
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/sys/performancedoctor/shenhe`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'status': this.dataForm.status
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
