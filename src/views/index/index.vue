<template>
  <div class="index-container">
    <el-row :gutter="20">
      <el-col :xs="24" :sm="24" :md="24" :lg="24" :xl="24">
        <el-card shadow="never">
          <div slot="header">
            <span>访问量</span>
          </div>
          <div class="svg-tree">
            <svg xmlns="http://www.w3.org/2000/svg" 
                :style="{transform: 'rotate('+ angle +'deg)'}" 
                stroke-width="1"
                :view-box.camel="viewbox">
                <g v-for="(item, i) in items" :key="item + i">

                  <cubic-bezier
                    :index="i"
                    :half-size="halfSize"
                    :top-height="topHeight"
                    :bottom-height="bottomHeight"
                    :radius="radius"
                    :distance="distance"
                    :item="item"
                    :svgStyle="svgStyle"
                  ></cubic-bezier>
                </g>
                
              <clip-mask
                :title="title"
                :half-size="halfSize"
                :top-height="topHeight"
                :radius="radius"
              ></clip-mask>
            </svg>
          </div>


        </el-card>
      </el-col>
      <el-col :xs="24" :sm="24" :md="12" :lg="6" :xl="6">
        <el-card shadow="never">
          <div slot="header">
            <span>访问量</span>
          </div>
          <vab-chart autoresize :options="fwl" />
          <div class="bottom">
            <span>
              日均访问量:

              <vab-count
                :start-val="config1.startVal"
                :end-val="config1.endVal"
                :duration="config1.duration"
                :separator="config1.separator"
                :prefix="config1.prefix"
                :suffix="config1.suffix"
                :decimals="config1.decimals"
              />
            </span>
          </div>
        </el-card>
      </el-col>
      <el-col :xs="24" :sm="24" :md="12" :lg="6" :xl="6">
        <el-card shadow="never">
          <div slot="header">
            <span>授权数</span>
          </div>
          <vab-chart autoresize :options="sqs" />
          <div class="bottom">
            <span>
              总授权数:
              <vab-count
                :start-val="config2.startVal"
                :end-val="config2.endVal"
                :duration="config2.duration"
                :separator="config2.separator"
                :prefix="config2.prefix"
                :suffix="config2.suffix"
                :decimals="config2.decimals"
              />
            </span>
          </div>
        </el-card>
      </el-col>

      <el-col
        v-for="(item, index) in iconList"
        :key="index"
        :xs="12"
        :sm="6"
        :md="3"
        :lg="3"
        :xl="3"
      >
        <router-link :to="item.link" target="_blank">
          <el-card class="icon-panel" shadow="never">
            <vab-icon
              :style="{ color: item.color }"
              :icon="['fas', item.icon]"
            ></vab-icon>
            <p>{{ item.title }}</p>
          </el-card>
        </router-link>
      </el-col>

      <el-col :xs="24" :sm="24" :md="12" :lg="12" :xl="12">
        <el-card class="card" shadow="never">
          <div slot="header">
            <span>依赖信息</span>
            <div style="float: right">部署时间:{{ updateTime }}</div>
          </div>
          <div class="bottom-btn">
            <el-popover placement="top" width="250" trigger="hover">
              <p>
                请我们喝杯咖啡，付款后联系qq
                783963206，我们将邀请您加入我们的讨论群，谢谢您愿意支持开源，加群获取文档、及基础模板，群内大佬众多，希望能帮到大家（如情况不允许，请勿勉强）。
              </p>
              <el-image :src="require('@/assets/zfb_kf.jpg')"></el-image>
              <a slot="reference" target="_blank">
                <el-button type="primary">QQ讨论群、基础版、文档</el-button>
              </a>
            </el-popover>
            <a
              target="_blank"
              href="https://github.com/chuzhixin/vue-admin-better"
            >
              <el-button type="warning">github下载源码点star</el-button>
            </a>
            <a
              target="_blank"
              href="https://gitee.com/chu1204505056/vue-admin-better"
            >
              <el-button type="warning">码云下载源码点star</el-button>
            </a>
            <a @click="handleChangeTheme">
              <el-button type="danger">修改主题和布局</el-button>
            </a>
          </div>
          <table class="table">
            <tr>
              <td>@vue/cli版本</td>
              <td>{{ devDependencies['@vue/cli-service'] }}</td>
              <td>vue版本</td>
              <td>{{ dependencies['vue'] }}</td>
            </tr>
            <tr>
              <td>vuex版本</td>
              <td>{{ dependencies['vuex'] }}</td>
              <td>vue-router版本</td>
              <td>{{ dependencies['vue-router'] }}</td>
            </tr>
            <tr>
              <td>element-ui版本</td>
              <td>{{ dependencies['element-ui'] }}</td>
              <td>axios版本</td>
              <td>{{ dependencies['axios'] }}</td>
            </tr>
            <tr>
              <td>eslint版本</td>
              <td>{{ devDependencies['eslint'] }}</td>
              <td>prettier版本</td>
              <td>{{ devDependencies['prettier'] }}</td>
            </tr>
            <tr>
              <td>sass版本</td>
              <td>{{ devDependencies['sass'] }}</td>
              <td>mockjs版本</td>
              <td>{{ dependencies['mockjs'] }}</td>
            </tr>
            <tr>
              <td>zx-layouts版本</td>
              <td>{{ dependencies['zx-layouts'] }}</td>
              <td>lodash版本</td>
              <td>{{ dependencies['lodash'] }}</td>
            </tr>
          </table>
        </el-card>

        <el-card shadow="never">
          <div slot="header">
            <span>其他信息</span>
          </div>
          <div style="text-align: center">
            <vab-colorful-icon style="font-size: 140px" icon-class="vab" />
            <h1 style="font-size: 30px">vue-admin-better</h1>
          </div>
          <div v-for="(item, index) in noticeList" :key="index">
            <el-alert
              v-if="index !== 0"
              :title="item.title"
              :type="item.type"
              :closable="item.closable"
            ></el-alert>
            <br />
          </div>
          <el-alert :closable="false" :title="userAgent" type="info"></el-alert>
          <br />
        </el-card>
      </el-col>

      <el-col :xs="24" :sm="24" :md="12" :lg="12" :xl="12">
        <el-card class="card" shadow="never">
          <div slot="header">
            <span>更新日志</span>
          </div>
          <el-timeline :reverse="reverse">
            <el-timeline-item
              v-for="(activity, index) in activities"
              :key="index"
              :timestamp="activity.timestamp"
              :color="activity.color"
            >
              {{ activity.content }}
            </el-timeline-item>
          </el-timeline>
        </el-card>
        <plan></plan>
        <version-information></version-information>
      </el-col>
    </el-row>
  </div>
</template>

<script>
  import VabChart from '@/plugins/echarts'
  import { dependencies, devDependencies } from '../../../package.json'
  import { getList } from '@/api/changeLog'
  import { getNoticeList } from '@/api/notice'
  import { getRepos, getStargazers } from '@/api/github'
  import Plan from './components/Plan'
  import VersionInformation from './components/VersionInformation'
  import ClipMask from "@/views/vab/svg/ClipMask.vue"
  import Config from "@/views/vab/svg/Config.vue"
  import CubicBezier from "@/views/vab/svg/CubicBezier.vue"


  export default {
    name: 'Index',
    components: {
      ClipMask,
      Config,
      CubicBezier,
      VabChart,
      Plan,
      VersionInformation,
    },
    data() {
      return {
        // svg
        title: "全国",
        size: 5000,
        angle: 0,
        items: [
          {name: '广州市',num: 88},
          {name: '深圳市',num: 85},
          {name: '东莞市',num: 82},
          {name: '香港'  ,num: 79},
          {name: '佛山市',num: 76},
          {name: '中山市',num: 74},
          {name: '江门市',num: 72},
          {name: '澳门'  ,num: 68},
          {name: '江门市',num: 68},
          {name: '江门市',num: 68},
          {name: '江门市',num: 68},
        ],
        svgStyle:{
          strokeWidth: 5,
          stroke: '#E3E6ED'
        },

        timer: 0,
        updateTime: process.env.VUE_APP_UPDATE_TIME,
        nodeEnv: process.env.NODE_ENV,
        dependencies: dependencies,
        devDependencies: devDependencies,
        config1: {
          startVal: 0,
          endVal: this.$baseLodash.random(20000, 60000),
          decimals: 0,
          prefix: '',
          suffix: '',
          separator: ',',
          duration: 8000,
        },
        config2: {
          startVal: 0,
          endVal: this.$baseLodash.random(1000, 20000),
          decimals: 0,
          prefix: '',
          suffix: '',
          separator: ',',
          duration: 8000,
        },
        config3: {
          startVal: 0,
          endVal: this.$baseLodash.random(1000, 20000),
          decimals: 0,
          prefix: '',
          suffix: '',
          separator: ',',
          duration: 8000,
        },

        //访问量
        fwl: {
          color: [
            '#1890FF',
            '#36CBCB',
            '#4ECB73',
            '#FBD437',
            '#F2637B',
            '#975FE5',
          ],
          backgroundColor: 'rgba(252,252,252,0)',
          grid: {
            top: '4%',
            left: '2%',
            right: '4%',
            bottom: '0%',
            containLabel: true,
          },
          xAxis: [
            {
              type: 'category',
              boundaryGap: false,
              data: [],
              axisTick: {
                alignWithLabel: true,
              },
            },
          ],
          yAxis: [
            {
              type: 'value',
            },
          ],
          series: [
            {
              name: '访问量',
              type: 'line',
              data: [],
              smooth: true,
              areaStyle: {},
            },
          ],
        },
        //授权数
        sqs: {
          color: [
            '#1890FF',
            '#36CBCB',
            '#4ECB73',
            '#FBD437',
            '#F2637B',
            '#975FE5',
          ],
          backgroundColor: 'rgba(252,252,252,0)',
          grid: {
            top: '4%',
            left: '2%',
            right: '4%',
            bottom: '0%',
            containLabel: true,
          },
          xAxis: [
            {
              type: 'category',
              /*boundaryGap: false,*/
              data: ['0时', '4时', '8时', '12时', '16时', '20时', '24时'],
              axisTick: {
                alignWithLabel: true,
              },
            },
          ],
          yAxis: [
            {
              type: 'value',
            },
          ],
          series: [
            {
              name: '授权数',
              type: 'bar',
              barWidth: '60%',
              data: [10, 52, 20, 33, 39, 33, 22],
            },
          ],
        },
        //词云
        cy: {
          grid: {
            top: '4%',
            left: '2%',
            right: '4%',
            bottom: '0%',
          },
          series: [
            {
              type: 'wordCloud',
              gridSize: 15,
              sizeRange: [12, 40],
              rotationRange: [0, 0],
              width: '100%',
              height: '100%',
              textStyle: {
                normal: {
                  color() {
                    const arr = [
                      '#5470c6',
                      '#91cc75',
                      '#fac858',
                      '#ee6666',
                      '#73c0de',
                      '#975FE5',
                    ]
                    let index = Math.floor(Math.random() * arr.length)
                    return arr[index]
                  },
                },
              },
              data: [
                {
                  name: 'vue-admin-better',
                  value: 15000,
                },
                {
                  name: 'element',
                  value: 10081,
                },
                {
                  name: 'beautiful',
                  value: 9386,
                },

                {
                  name: 'vue',
                  value: 6500,
                },
                {
                  name: 'chuzhixin',
                  value: 6000,
                },
                {
                  name: 'good',
                  value: 4500,
                },
                {
                  name: 'success',
                  value: 3800,
                },
                {
                  name: 'never',
                  value: 3000,
                },
                {
                  name: 'boy',
                  value: 2500,
                },
                {
                  name: 'girl',
                  value: 2300,
                },
                {
                  name: 'github',
                  value: 2000,
                },
                {
                  name: 'hbuilder',
                  value: 1900,
                },
                {
                  name: 'dcloud',
                  value: 1800,
                },
                {
                  name: 'china',
                  value: 1700,
                },
                {
                  name: '1204505056',
                  value: 1600,
                },
                {
                  name: '972435319',
                  value: 1500,
                },
                {
                  name: 'young',
                  value: 1200,
                },
                {
                  name: 'old',
                  value: 1100,
                },
                {
                  name: 'vuex',
                  value: 900,
                },
                {
                  name: 'router',
                  value: 800,
                },
                {
                  name: 'money',
                  value: 700,
                },
                {
                  name: 'qingdao',
                  value: 800,
                },
                {
                  name: 'yantai',
                  value: 9000,
                },
                {
                  name: 'author is very cool',
                  value: 9200,
                },
              ],
            },
          ],
        },

        //更新日志
        reverse: true,
        activities: [],
        noticeList: [],
        //其他信息
        userAgent: navigator.userAgent,
        //卡片图标
        iconList: [
          {
            icon: 'video',
            title: '视频播放器',
            link: '/vab/player',
            color: '#ffc069',
          },
          {
            icon: 'table',
            title: '表格',
            link: '/vab/table/comprehensiveTable',
            color: '#5cdbd3',
          },
          {
            icon: 'laptop-code',
            title: '源码',
            link: 'https://github.com/chuzhixin/vue-admin-better',
            color: '#b37feb',
          },
          {
            icon: 'book',
            title: '书籍',
            link: '',
            color: '#69c0ff',
          },
          {
            icon: 'bullhorn',
            title: '公告',
            link: '',
            color: '#ff85c0',
          },
          {
            icon: 'gift',
            title: '礼物',
            link: '',
            color: '#ffd666',
          },

          {
            icon: 'balance-scale-left',
            title: '公平的世界',
            link: '',
            color: '#ff9c6e',
          },
          {
            icon: 'coffee',
            title: '休息一下',
            link: '',
            color: '#95de64',
          },
        ],
      }
    },
    created() {
      this.fetchData()
    },
    beforeDestroy() {
      clearInterval(this.timer)
    },
    mounted() {
      let base = +new Date(2020, 1, 1)
      let oneDay = 24 * 3600 * 1000
      let date = []

      let data = [Math.random() * 1500]
      let now = new Date(base)

      const addData = (shift) => {
        now = [now.getFullYear(), now.getMonth() + 1, now.getDate()].join('/')
        date.push(now)
        data.push(this.$baseLodash.random(20000, 60000))

        if (shift) {
          date.shift()
          data.shift()
        }

        now = new Date(+new Date(now) + oneDay)
      }

      for (let i = 1; i < 6; i++) {
        addData()
      }
      addData(true)
      this.fwl.xAxis[0].data = date
      this.fwl.series[0].data = data
      this.timer = setInterval(() => {
        addData(true)
        this.fwl.xAxis[0].data = date
        this.fwl.series[0].data = data
      }, 3000)
    },
    methods: {
      handleClick(e) {
        this.$baseMessage(`点击了${e.name},这里可以写跳转`)
      },
      handleZrClick(e) {},
      handleChangeTheme() {
        this.$baseEventBus.$emit('theme')
      },
      async fetchData() {
        const { data } = await getList()
        data.map((item, index) => {
          if (index === data.length - 1) {
            item.color = '#0bbd87'
          }
        })
        this.activities = data
        const res = await getNoticeList()
        this.noticeList = res.data
        /* getRepos({
        token: "1061286824f978ea3cf98b7b8ea26fe27ba7cea1",
      }).then((res) => {
        const per_page = Math.ceil(res.data.stargazers_count / 100);
        alert(per_page);
        getStargazers({
          token: "1061286824f978ea3cf98b7b8ea26fe27ba7cea1",
          page: 1,
          per_page: res.per_page,
        }).then((res) => {
          alert(JSON.stringify(res));
        });
      }); */
      },
    },
    computed: {
      topHeight() {
        // 20% of the size
        return this.size * 0.2
      },
      bottomHeight() {
        // 80% of the size
        return this.size * 0.4
      },
      width() {
        return this.size
      },
      halfSize() {
        // 50% of the size
        return this.size * 0.5
      },
      distance() {
        // distance between two array items on the horizon
        return Math.round(this.width / this.items.length)
      },
      // xPosition() {
      //   return i => {
      //      return this.distance * i + this.distance * 0.5
      //   };     
      // },
      radius() {
        return this.topHeight * 0.3
      },
      viewbox() {
        return "0 0 " + this.size  + " " + this.size * 0.5
      }
    },

  }
</script>
<style>
  .svg-tree{
    width: 100%;
    height: 400px;
  }
  .item {
    stroke: #ccc;
    stroke-width: 5;
  }
  path {
    stroke: #5bbaa1;
    fill: none;
    stroke-width: 3;
    stroke-linecap: round;
  }
  text {
    font-size: 28px;
    text-anchor: middle;
  }
  #container {
    display: flex;
    margin: auto;
    height: 100vh;
    /* width: 1000px;
    height: 500px; */
    justify-content: center;
    border: 2px solid #b2ebf2;
    border-radius: 10px;
  }
  #config {
    padding: 10px;
    margin: 5px;
    height: fit-content;
    background: #82e5cb;
    border-radius: 5px;
  }
  svg {
    width: 100%;
    height: 100%;
  }

  .title {
    font-size: 40px;
    font-weight: bold;
    font-style: italic;
    color: #2389EF;
  }
  .clip-svg {
    clip-path: url(#myClip);
  }
  #config button {
    float: right;
  }
  .item {
    border-bottom: 1px dashed rgba(0, 0, 0, 0.5);
    padding: 5px;
  }
  .action {
    padding: 10px;
  }
  input,
  button {
    margin-bottom: 3px;
  }
  .circle {
    stroke-width: 5;
    stroke: #5bbaa1;
    fill: none;
  }
</style>
<style lang="scss" scoped>
  .index-container {
    padding: 0 !important;
    margin: 0 !important;
    background: #f5f7f8 !important;

    ::v-deep {
      .el-alert {
        padding: $base-padding;

        &--info.is-light {
          min-height: 82px;
          padding: $base-padding;
          margin-bottom: 15px;
          color: #909399;
          background-color: $base-color-white;
          border: 1px solid #ebeef5;
        }
      }

      .el-card__body {
        .echarts {
          width: 100%;
          height: 115px;
        }
      }
    }

    .card {
      ::v-deep {
        .el-card__body {
          .echarts {
            width: 100%;
            height: 305px;
          }
        }
      }
    }

    .bottom {
      padding-top: 20px;
      margin-top: 5px;
      color: #595959;
      text-align: left;
      border-top: 1px solid $base-border-color;
    }

    .table {
      width: 100%;
      color: #666;
      border-collapse: collapse;
      background-color: #fff;

      td {
        position: relative;
        min-height: 20px;
        padding: 9px 15px;
        font-size: 14px;
        line-height: 20px;
        border: 1px solid #e6e6e6;

        &:nth-child(odd) {
          width: 20%;
          text-align: right;
          background-color: #f7f7f7;
        }
      }
    }

    .icon-panel {
      height: 117px;
      text-align: center;
      cursor: pointer;

      svg {
        font-size: 40px;
      }

      p {
        margin-top: 10px;
      }
    }

    .bottom-btn {
      button {
        margin: 5px 10px 15px 0;
      }
    }
  }
</style>
