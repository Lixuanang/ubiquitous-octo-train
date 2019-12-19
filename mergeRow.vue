<!-- 报价明细  -->

<template>
  <div class="page-container ">
      <el-table
        v-loading="loading"
        :data="tableData"
        :span-method="objectSpanMethod"
        border
      >
        <el-table-column
          v-for="q in queryData"
          :key="q.prop"
          header-align="center"
          align="center"
          :prop="q.prop"
          :label="q.label"
          :width="q.width"
          :min-width="q.minWidth"
        >
        </el-table-column>
      </el-table>
  </div>
</template>

<script>
import { getOfferDetail } from '@/api/userData'
export default {
  components: { Pagination },
  props: {},
  data() {
    return {
      queryData: [
        { prop: 'inquiryNo', label: '询价单号', minWidth: '190' },
        { prop: 'typeText', label: '报价方式' },
        { prop: 'fleetName', label: '车队名称' },
        { prop: 'reportName', label: '报价人' },
        { prop: 'createTime', label: '报价时间', minWidth: '135' },
        { prop: 'reportMoney', label: '报价' },
        { prop: 'reportCarNum', label: '承运车数' }
      ],
      tableData: []
    }
  },
  computed: {},
  watch: {},
  created() {
    this.page = +sessionStorage.getItem('page')
  },
  mounted() {
    this.init()
  },
  methods: {
    // 列表
    getOfferDetail() {
      return getOfferDetail({ ...this.filterData, page: this.page })
        .then(res => {
          this.tableData = this.mergeTableRow(res.list, ['inquiryNo']) // 表格行合并
        })
        .finally(() => {
          this.loading = false
        })
    },
    // 表格行合并
    objectSpanMethod({ row, column, rowIndex, columnIndex }) {
      const span = column.property + '-span'
      if (row[span]) {
        return row[span]
      }
    },
    /**
 * element表格合并行列
 * @param {Array} data 表格数据
 * @param {Array} merge 合并字段
 */
 function mergeTableRow(data, merge) {
  if (!merge || merge.length === 0) {
    return data
  }
  merge.forEach(m => {
    const mList = {}
    data = data.map((v, index) => {
      const rowVal = v[m]
      if (mList[rowVal]) {
        mList[rowVal]++
        data[index - (mList[rowVal] - 1)][m + '-span'].rowspan++
        v[m + '-span'] = {
          rowspan: 0,
          colspan: 0
        }
      } else {
        mList[rowVal] = 1
        v[m + '-span'] = {
          rowspan: 1,
          colspan: 1
        }
      }
      return v
    })
  })
  return data
}
  }
}
</script>

<style lang="scss">
</style>
