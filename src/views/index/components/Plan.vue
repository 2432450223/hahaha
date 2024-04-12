<template>
  <el-card shadow="hover">
    <template #header>
      <vab-icon icon="send-plane-2-line" />
      <!-- 计划 -->
      <el-tag class="card-header-tag" type="success">祝小区居民都能住上小别墅</el-tag>
    </template>
    <el-table :data="tableData" height="283px" row-key="title">
      <el-table-column align="center" label="拖拽" width="50px">
        <template #default="{}">
          <vab-icon :icon="['fas', 'arrows-alt']" style="cursor: pointer" />
        </template>
      </el-table-column>
      <el-table-column width="20px" />
      <el-table-column label="环境监测" prop="title" width="230px" />
      <el-table-column label="监测数据" width="220px">
        <template #default="{ row }">
          <el-progress :color="row.color" :percentage="row.percentage" />
        </template>
      </el-table-column>
      <el-table-column width="50px" />
      <el-table-column label="完成时间" prop="endTIme" />
    </el-table>
  </el-card>
</template>
<script>
  import Sortable from 'sortablejs'

  export default {
    data() {
      return {
        tableData: [
          {
            title: 'PM2.5',
            endTIme: '2024-4-07-8:00',
            percentage: 2.5,
            color: '#95de64',
          },
          {
            title: 'CO2',
            endTIme: '2024-4-08-8:00',
            percentage: 21,
            color: '#69c0ff',
          },
          {
            title: 'TVOC',
            endTIme: '2024-4-08-8:00',
            percentage: 20,
            color: '#1890FF',
          },
          {
            title: '湿度',
            endTIme: '2024-4-09-8:00',
            percentage: 79,
            color: '#ffc069',
          },
          {
            title: '温度',
            endTIme: '2024-4-09-8:01',
            percentage: 25,
            color: '#5cdbd3',
          },
          {
            title: '噪音',
            endTIme: '2024-4-09-8:01',
            percentage: 33,
            color: '#b37feb',
          },
        ],
      }
    },
    mounted() {
      const tbody = document.querySelector('.el-table__body-wrapper tbody')
      const _this = this
      Sortable.create(tbody, {
        onEnd({ newIndex, oldIndex }) {
          const currRow = _this.tableData.splice(oldIndex, 1)[0]
          _this.tableData.splice(newIndex, 0, currRow)
        },
      })
    },
  }
</script>
