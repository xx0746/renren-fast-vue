<template>
  <el-dialog
    title="上传文件"
    :close-on-click-modal="false"
    @close="closeHandle"
    :visible.sync="visible">
    <el-upload
      drag
      :action="url"
      :before-upload="beforeUploadHandle"
      :on-success="successHandle"
      multiple
      :file-list="fileList"
      style="text-align: center;">
      <i class="el-icon-upload"></i>
      <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
    </el-upload>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        url: '',
        num: 0,
        successNum: 0,
        fileList: []
      }
    },
    methods: {
      init (id) {
        this.url = this.$http.adornUrl(`/sys/user/uploadExcel?token=${this.$cookie.get('token')}`)
        this.visible = true
      },
      // 上传之前
      beforeUploadHandle (file) {

      },
      // 上传成功
      successHandle (response, file, fileList) {
        if (response && response.code === 0) {
          this.$message.info('导入成功')
          this.visible = false
        } else {
          this.$message.error(response.msg)
        }
      },
      // 弹窗关闭时
      closeHandle () {

      }
    }
  }
</script>
