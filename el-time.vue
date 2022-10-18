<template>
  <div>
   	<el-table-column
                  prop="times"
                  :label="$t('hospset.testTime')"
                  align="left"
                  header-align="center"
                >
                  <template slot-scope="scope">
                    <!-- <el-time-picker
                      v-for="(item, index) in scope.row.times"
                      :key="index"
                      v-model="item.time"
                      style="width: 120px;"
                      value-format="HH:mm"
                      :clearable="false"
                      :readonly="!scope.row.isEdit"
                      :disabled="!scope.row.isEdit"
                      :picker-options="{
                        selectableRange: `${item.startTime}-${item.endTime}`
                      }"
                    /> -->
                    <el-time-select
                      v-for="(item, index) in scope.row.times"
                      :key="index"
                      v-model="item.time"
                      style="width: 120px;"
                      value-format="HH:mm"
                      :clearable="false"
                      :readonly="!scope.row.isEdit"
                      :disabled="!scope.row.isEdit"
                      :picker-options="{
                        start: item.startTime,
                        step: '00:15',
                        end: item.endTime
                      }"
                    />
                  </template>
                </el-table-column>
  </div>
</template>
<script>
import { Message } from 'element-ui'
export default {
  name: 'Setting',
  data () 
    return {
      
    }
  },
  methods: {
    inputLimit (e) {
      const key = e.key
      // 不允许输入'e'和'.'、'+'、'-'、'Shift'
      if (key === 'e' || key === '+' || key === '-' || key === 'Shift') {
        e.returnValue = false
        return false
      }
      return true
    },
    // 医嘱模板设置
    orderTempSearch () {
      getLongNotStandardsData('LongNotStandards').then(res => {
        if (res.dataContent !== null) {
          res.dataContent.forEach(item => {
            item['isEdit'] = false
            const pp = []
            item.times.forEach(itema => {
              console.log(itema)
              const obj = {}
              // obj.startTime = itema + ':00'
              obj.startTime = itema
              obj.time = itema
              // obj.endTime = itema.split(':')[0] + ':59:00'
              obj.endTime = itema.split(':')[0] + ':59'
              pp.push(obj)
            })
            item.times = pp
          })
          this.orderTempTable = res.dataContent
        }
      }).catch(err => {
        Message.error(err.message)
      })
    },
    handleOrderTempEdit (row) {
      row.isEdit = true
      console.log(row)
    },
    handleOrderTempConfirm (row) {
      console.log(row)
      const arr = []
      if (row.times) {
        row.times.forEach(item => {
          arr.push(item.time)
        })
      }
      const data = {
        code: row.code,
        name: row.name,
        times: arr
      }
      editLongNotStandardsTime(data).then(res => {
        Message.success(this.$t('hospset.editSucc'))
        row.isEdit = false
        this.orderTempSearch()
      }).catch(err => {
        Message.error(err.message)
      })
    },
    handleOrderTempCancle (row) {
      row.isEdit = false
      this.orderTempSearch()
    }
  }
}
</script>