<template>
  <div>
    <el-row>
      <el-col :span="24">
        <div class="user-addcount">
          <el-form :model="pwdModify" label-width="80px" ref="modifyPwdForm">
            <el-form-item :minlength="6" label="原密码" prop="password">
              <el-input show-password v-model="pwdModify.password"></el-input>
            </el-form-item>
            <el-form-item :minlength="6" label="新密码" prop="newPassword">
              <el-input show-password v-model="pwdModify.newPassword"></el-input>
            </el-form-item>
            <el-form-item :minlength="6" label="确认密码" prop="confirmPassword">
              <el-input show-password v-model="pwdModify.confirmPassword"></el-input>
            </el-form-item>
          </el-form>
          <div class="dialog-footer" slot="footer">
            <el-button @click="savePassword" type="primary">确 定</el-button>
          </div>
        </div>
      </el-col>
    </el-row>

  </div>
</template>
<script>
  export default {
    data () {
      return {
        userInfo: {},
        activeName: 'first',
        pwdModify: {}
      }
    },
    methods: {
      // 表单提交
      handleClick (tab, event) {
        console.log(11111)
      },
      savePassword () {
        if (this.pwdModify.newPassword !== this.pwdModify.confirmPassword) {
          this.$message.error('两次密码输入不同')
        }
        this.$http({
          url: this.$http.adornUrl('/sys/user/password'),
          method: 'post',
          data: this.$http.adornData({
            'password': this.pwdModify.password,
            'newPassword': this.pwdModify.newPassword
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.$message({
              message: '操作成功',
              type: 'success'
            })
            this.pwdModify = {}
          } else {
            this.$message.error(data.msg)
          }
        })
      }
    },
    created () {
      this.$http({
        url: this.$http.adornUrl('/sys/user/info'),
        method: 'get'
      }).then(({data}) => {
        this.userInfo = data.user
      })
    }
  }
</script>
<style lang="scss">
  .avatar-box {
    box-shadow: -2px 0 20px -16px;
    width: 80%;
    height: 100%;
    .user-avatar{
      cursor: pointer;
    }
    .user-card {
      min-height: calc(90vh - 200px);
      padding: 30px 20px;
      text-align: center;
      .el-avatar {
        border-radius: 50%;
      }
      .user-personality {
        padding: 24px 0;
        text-align: center;
        p {
          font-size: 16px;
        }
        .nickname {
          font-size: 26px;
        }
        .person-info{
          margin-top: 6px;
          font-size: 14px;
          color:#999
        }
      }
      .user-information {
        width: 100%;
        height: 100%;
        text-align: left;
        ul {
          display: inline-block;
          height: 100%;
          li {
            i {
              margin-right: 8px;
            }
            padding: 20px 0;
            font-size: 16px;
            font-weight: 400;
            color: #606266;
          }
        }
      }
    }
  }
  .user-addcount {
    ul {
      li {
        .title {
          padding: 10px;
          font-size: 18px;
          color: #696969;
        }
        .desc {
          font-size: 16px;
          padding: 0 10px 20px 10px;
          color: #a9a9a9;
          a {
            color: rgb(64, 158, 255);
            float: right;
          }
        }
        border-bottom: 2px solid #f0f2f5;
      }
    }
  }
  .el-tab-pane{
    display: block;
  }
</style>
