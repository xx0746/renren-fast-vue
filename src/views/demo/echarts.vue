<template>
  <div class="mod-demo-echarts">
<!--    <el-alert-->
<!--      title="提示："-->
<!--      type="warning"-->
<!--      :closable="true">-->
<!--&lt;!&ndash;      <p class="el-alert__description">1. 此Demo只提供ECharts官方使用文档，入门部署和体验功能。具体使用请参考：http://echarts.baidu.com/index.html</p>&ndash;&gt;-->

<!--&lt;!&ndash;      <div slot-scope="description">&ndash;&gt;-->
<!--&lt;!&ndash;      </div>&ndash;&gt;-->
<!--    </el-alert>-->

    <el-row :gutter="20">
      <el-col :span="24">
        <el-card>
          <div id="J_chartLineBox" class="chart-box"></div>
        </el-card>
      </el-col>
      <el-col :span="24">
        <el-card>
          <div id="G_chartLineBox" class="chart-box"></div>
        </el-card>
      </el-col>
      <el-col :span="24">
        <el-card>
          <div id="H_chartLineBox" class="chart-box"></div>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
  import echarts from 'echarts'
  export default {
    data () {
      return {
        chartLine: null,
        chartGLine: null,
        chartHLine: null,
        chartBar: null,
        chartPie: null,
        chartScatter: null,
        dataList: []

      }
    },
    mounted () {
      this.initEchart()
      this.initChartLine()
      this.initGChartLine()
      this.initHChartLine()
      // this.initChartBar()
      // this.initChartPie()
      // this.initChartScatter()
    },
    // beforeMount () {
    //   this.initEchart()
    // },
    activated () {
      // 由于给echart添加了resize事件, 在组件激活时需要重新resize绘画一次, 否则出现空白bug
      if (this.chartLine) {
        this.chartLine.resize()
      }
    },
    methods: {
      initEchart () {
        this.$http({
          url: this.$http.adornUrl('/sys/performance/echarts'),
          method: 'get',
        }).then(({data}) => {
          if (data && data.code === 0) {
            console.log("into")
            this.dataList = data.data;
            var option = {
              title: {
                text: '部门人员统计'
              },
              tooltip: {},
              legend: {
                data:['人数']
              },
              xAxis: {
                data: ["科研中心领导","职能部门领导","专家博士","办公室","科研管理部","人力资源部","院领导","科研中心员工","职能员工"]
              },
              yAxis: {},
              series: [{
                name: '人数',
                type: 'bar',
                data: []
              }]
            };
            option.series[0].data=this.dataList
            this.chartLine.setOption(option)
            this.chartLine.resize()
          } else {
            this.$message.error(data.msg)
          }
        });


        //通过率
        this.$http({
          url: this.$http.adornUrl('/sys/performance/echartstu'),
          method: 'get',
        }).then(({data}) => {
          if (data && data.code === 0) {
            var option = {
              title: {
                text: '绩效审核'
              },
              tooltip: {},
              legend: {
                data:['数量']
              },
              xAxis: {
                data: ["通过","驳回"]
              },
              yAxis: {},
              series: [{
                name: '数量',
                type: 'bar',
                data: []
              }]
            };
            option.series[0].data = data.data;
            this.chartGLine.setOption(option)
            this.chartGLine.resize()
          } else {
            this.$message.error(data.msg)
          }
        });



        //得分情况
        this.$http({
          url: this.$http.adornUrl('/sys/performance/echartScore'),
          method: 'get',
        }).then(({data}) => {
          if (data && data.code === 0) {
            var option = {
              title: {
                text: '绩效得分'
              },
              tooltip: {},
              legend: {
                data:['数量']
              },
              xAxis: {
                data: ["A","B","C"]
              },
              yAxis: {},
              series: [{
                name: '数量',
                type: 'bar',
                data: []
              }]
            };
            option.series[0].data = data.data;
            this.chartHLine.setOption(option)
            this.chartHLine.resize()
          } else {
            this.$message.error(data.msg)
          }
        });
      },
      // 折线图
      initChartLine () {
        var option = {
          title: {
            text: '部门人员统计'
          },
          tooltip: {},
          legend: {
            data:['人数']
          },
          xAxis: {
            data: ["科研中心领导","职能部门领导","专家博士","办公室","科研管理部","人力资源部","院领导","科研中心员工","职能员工"]
          },
          yAxis: {},
          series: [{
            name: '人数',
            type: 'bar',
            data: []
          }]
        };
        this.chartLine = echarts.init(document.getElementById('J_chartLineBox'))
        // console.log('111'+this.dataList)

        this.chartLine.setOption(option)
        // window.addEventListener('resize', () => {
        //   this.chartLine.resize()
        // })
      },
      initGChartLine () {
        var option = {
          title: {
            text: '绩效审核'
          },
          tooltip: {},
          legend: {
            data:['数量']
          },
          xAxis: {
            data: ["通过","驳回"]
          },
          yAxis: {},
          series: [{
            name: '数量',
            type: 'bar',
            data: []
          }]
        };
        this.chartGLine = echarts.init(document.getElementById('G_chartLineBox'))

        this.chartGLine.setOption(option)

      },
      initHChartLine () {
        var option = {
          title: {
            text: '绩效得分'
          },
          tooltip: {},
          legend: {
            data:['数量']
          },
          xAxis: {
            data: ["A","B","C"]
          },
          yAxis: {},
          series: [{
            name: '数量',
            type: 'bar',
            data: []
          }]
        };
        this.chartHLine = echarts.init(document.getElementById('H_chartLineBox'))

        this.chartHLine.setOption(option)

      },

    }
  }
</script>

<style lang="scss">
  .mod-demo-echarts {
    > .el-alert {
      margin-bottom: 10px;
    }
    > .el-row {
      margin-top: -10px;
      margin-bottom: -10px;
      .el-col {
        padding-top: 10px;
        padding-bottom: 10px;
      }
    }
    .chart-box {
      min-height: 400px;
    }
  }
</style>
