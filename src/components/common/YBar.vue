<template>
  <div class="y-bar" ref="y-bar" :style="{width, height}"></div>
</template>

<script>
  export default {
    name: 'YBar',
    props: {
      width: {
        type: [Number, String],
        default: '100%'
      },
      height: {
        type: [Number, String],
        default: '100%'
      },
      processCode: {
        type: String,
        required: true
      }
    },
    data () {
      return {
        myChart: null
      }
    },
    // watch: {
    //   'processCode': 'fetchData'
    // },
    methods: {
      init () {
        this.myChart = this.echarts.init(this.$refs['y-bar'])

        let option = {
          legend: {
            orient: 'vertical',
            data: ['计划', '达成'],
            right: 20,
            top: 'middle'
          },
          grid: {
            top: '1%',
            right: '10%',
            bottom: '1%',
            left: '1%',
            containLabel: true
          },
          xAxis: {
          },
          yAxis: {
            data: [],
            axisLabel: {
              rotate: 0,
              textStyle: {
                fontSize: 30,
                fontWeight: 'bold'
              }
            },
            axisTick: {
              length: 999
            }
          },
          color: ['#418ebd', '#44c57e', '#c23531', '#c4ccd3'],
          // animation: false,
          series: [{
            name: '计划',
            type: 'bar',
            label: {
              normal: {
                show: true,
                position: 'right',
                textStyle: {
                  fontSize: 30,
                  fontWeight: 'bold'
                }
              }
            }
          }, {
            name: '达成',
            type: 'bar',
            label: {
              normal: {
                show: true,
                position: 'right',
                textStyle: {
                  fontSize: 30,
                  fontWeight: 'bold'
                }
              }
            }
          }]
        }
        this.myChart.setOption(option)
      },
      fetchData () {
        this.$http.get(`/DataAPI/ProduceReport/processInOutNg.ashx?ActType=GetOrderPlan&process_code=${this.processCode}`).then(res => {
          let dataList = res.data.orderplan
          this.myChart.setOption({
            yAxis: {
              data: dataList.map(item => `${item.orderno.split('-')[0]}`)
            },
            series: [{
              data: dataList.map(item => item.planqty)
            }, {
              data: dataList.map(item => item.initqty)
            }]
          })
        }).catch(err => {
          console.log(err)
        })
      }
    },
    mounted () {
      console.log('y-bar mounted')
      this.init()
    },
    activated () {
      console.log('y-bar activated')
      this.processCode && this.fetchData()
    },
    deactivated () {
      this.myChart.setOption({
        yAxis: {
          data: []
        },
        series: [{
          data: []
        }, {
          data: []
        }]
      })
    }
  }
</script>

<style lang="css" scoped>
</style>
