<template>
  <div class="index-container">
    <el-row :gutter="20">
      <el-col :lg="24" :md="24" :sm="24" :xl="24" :xs="24">
        <el-alert v-if="noticeList[0]" :closable="noticeList[0].closable">
          <div style="display: flex; align-items: center; justify-content: center">
            <a href="https://github.com/chuzhixin/vue-admin-better" target="_blank">
              <img
                src="https://img.shields.io/github/stars/chuzhixin/vue-admin-better?style=flat-square&label=Stars&logo=github"
                style="margin-right: 10px"
              />
            </a>
            <p v-html="noticeList[0].title"></p>
          </div>
        </el-alert>
      </el-col>
      <el-col :lg="6" :md="12" :sm="24" :xl="6" :xs="24">
        <el-card shadow="never">
          <div slot="header">
            <span>小区访问量</span>
          </div>
          <vab-chart autoresize :option="fwl" />
          <div class="bottom">
            <span>
              日均访问量:

              {{ config1.endVal }}
            </span>
          </div>
        </el-card>
      </el-col>
      <el-col :lg="6" :md="12" :sm="24" :xl="6" :xs="24">
        <el-card shadow="never">
          <div slot="header">
            <span>授权数</span>
          </div>
          <vab-chart autoresize :option="sqs" />
          <div class="bottom">
            <span>
              总授权数:
              {{ config2.endVal }}
            </span>
          </div>
        </el-card>
      </el-col>

      <el-col v-for="(item, index) in iconList" :key="index" :lg="3" :md="3" :sm="6" :xl="3" :xs="12">
        <router-link target="_blank" :to="item.link">
          <el-card class="icon-panel" shadow="never">
            <vab-icon :icon="['fas', item.icon]" :style="{ color: item.color }" />
            <p>{{ item.title }}</p>
          </el-card>
        </router-link>
      </el-col>

      <el-col :lg="11" :md="24" :sm="24" :xl="11" :xs="24">
        <el-card class="card" shadow="never">
          <div slot="header">
            <span>依赖信息</span>
            <div style="float: right">部署时间:{{ updateTime }}</div>
          </div>
          <div class="bottom-btn">
            <el-popover placement="top" trigger="hover" width="250">
              <!-- <p>
                请我们喝杯咖啡，付款后联系qq
                783963206，我们将邀请您加入我们的讨论群，谢谢您愿意支持开源，加群获取文档、及基础模板，群内大佬众多，希望能帮到大家（如情况不允许，请勿勉强）。
              </p> -->
              <!-- <el-image :src="require('@/assets/zfb_kf.jpg')" /> -->
              <a slot="reference" target="_blank">
                <el-button type="primary">小区智慧互联</el-button>
              </a>
            </el-popover>
            <a href="https://github.com/chuzhixin/vue-admin-better" target="_blank">
              <el-button type="warning">传感器的应用</el-button>
            </a>
            <a href="https://gitee.com/chu1204505056/vue-admin-beautiful" target="_blank">
              <el-button type="warning">环境监测</el-button>
            </a>
            <a href="https://github.com/chuzhixin/vue-admin-arco" target="_blank">
              <el-button type="warning">设施检测</el-button>
            </a>
            <a @click="handleChangeTheme">
              <el-button type="danger">安全报警</el-button>
            </a>
          </div>
          <table class="table">
            <!-- <tr>
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
              <td>layouts版本</td>
              <td>{{ dependencies['layouts'] }}</td>
              <td>lodash版本</td>
              <td>{{ dependencies['lodash'] }}</td>
            </tr> -->
          </table>
        </el-card>

        <el-card shadow="never">
          <div slot="header">
            <span>其他信息</span>
          </div>
          <div style="text-align: center">
            <vab-colorful-icon icon-class="vab" style="font-size: 140px" />
            <h1 style="font-size: 30px">小区智慧互联</h1>
          </div>
          <div v-for="(item, index) in noticeList" :key="index">
            <el-alert v-if="index !== 0" :closable="item.closable" :title="item.title" :type="item.type" />
            <br />
          </div>
          <el-alert :closable="false" :title="userAgent" type="info" />
          <br />
        </el-card>
      </el-col>

      <el-col :lg="13" :md="13" :sm="24" :xl="13" :xs="24">
        <el-card class="card" shadow="never">
          <div slot="header">
            <span>更新日志</span>
          </div>
          <el-timeline :reverse="reverse">
            <el-timeline-item v-for="(activity, index) in activities" :key="index" :color="activity.color" :timestamp="activity.timestamp">
              {{ activity.content }}
            </el-timeline-item>
          </el-timeline>
        </el-card>
        <plan />
        <version-information />
      </el-col>
    </el-row>
  </div>
</template>

<script>
  import VabChart from '@/plugins/echarts'
  import { dependencies, devDependencies } from '../../../package.json'
  import { getList } from '@/api/changeLog'
  import { getNoticeList } from '@/api/notice'
  import Plan from './components/Plan'
  import VersionInformation from './components/VersionInformation'

  export default {
    name: 'Index',
    components: {
      VabChart,
      Plan,
      VersionInformation,
    },
    data() {
      return {
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
          color: ['#1890FF', '#36CBCB', '#4ECB73', '#FBD437', '#F2637B', '#975FE5'],
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
          color: ['#1890FF', '#36CBCB', '#4ECB73', '#FBD437', '#F2637B', '#975FE5'],
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
                    const arr = ['#5470c6', '#91cc75', '#fac858', '#ee6666', '#73c0de', '#975FE5']
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
        // //其他信息
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
      handleZrClick() {},
      handleChangeTheme() {
        this.$baseEventBus.$emit('theme')
      },
      async fetchData() {
        const { data } = await getList()
        // const { data } = {"code":200,"msg":"","data":[{"_id":"60fd6dbecd84d60001f99f33","content":"在github上获得了第一个star，感恩一位名叫Bequiet2014的github用户。","timestamp":"2020-03-23"},{"_id":"60fd6defcd84d60001f9a039","content":"品牌升级：付费版vue2.x品牌升级为Admin Pro，付费版vue3.x品牌名升级为Admin Plus。","timestamp":"最近更新"},{"_id":"610978795c2db80001a09dc5","content":"团结一心，共抗疫情，烟台加油！！","timestamp":"最近更新"},{"_id":"6117518b7c33d50001d1ab10","content":"七夕当天github标星破万，感恩有你，愿你有人疼有人爱，开心度过每一天。","timestamp":"最近更新"},{"_id":"612e2ecb1b51d10001b8f1ad","content":"没有老师就没有vab的今天，提前预祝所有老师教师节快乐，预祝所有用户中秋快乐。","timestamp":"最近更新"},{"_id":"61b6e72cd00c060001339c27","content":"忘记历史就意味着背叛，否认罪责就意味着重犯。一切罔顾侵略战争历史的态度，一切美化侵略战争性质的言论，不论说了多少遍，不论说得多么冠冕堂皇，都是对人类和平和正义的侵害。对这些错误言行，爱好和平与正义的人们必须高度警惕、坚决反对。","timestamp":"南京大屠杀公祭日"},{"_id":"61c4752c39bf1000016e4439","content":"冬已至，春不远！众志成城，攻坚克难！愿长安，长长安！西安加油！","timestamp":"最近更新"},{"_id":"621ce37f3da49e0001b419b1","content":"没有人是为了战争而生，也不该又有人因为疫病而死，愿世间重归和平，愿病毒早日驱散。","timestamp":"最近更新"},{"_id":"6252ef47eb124700019b4977","content":"全民抗疫，众志成城，共克时艰，广州加油！中国加油！","timestamp":"最近更新"},{"_id":"63874f37ce2777000176d727","content":"沉痛缅怀，深切哀悼，伟人千古！","timestamp":"2022-11-30"},{"_id":"646f759b09e2989198390163","content":"身处裁员潮中的我见证了太多的无可奈何，我不评论公司选择人才的标准，只希望大家振作起来，争取体面的活着，加油！","timestamp":"2023-05-25"},{"_id":"648079730c801c6b97f35577","content":"高考不是终点，人生的路还很漫长，努力去活成自己喜欢的样子，致敬这必死无疑的一生！","timestamp":"2023-06-07"},{"_id":"64e826b86e5d2d8f64e1bb3a","content":"历史不会忘记，2023年8月24日，日本不顾全人类生命安全与世界关切公然将核污水排海！","timestamp":"2023-08-24"}]}
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
  }
</script>
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
