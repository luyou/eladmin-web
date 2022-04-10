<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
        <label class="el-form-item-label">店铺ID</label>
        <el-input v-model="query.sellingPartnerId" clearable placeholder="店铺ID" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">报告ID</label>
        <el-input v-model="query.reportId" clearable placeholder="报告ID" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">订单ID</label>
        <el-input v-model="query.orderId" clearable placeholder="订单ID" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <br />
        <label class="el-form-item-label">商城代码</label>
        <el-select v-model="query.marketplaceId" filterable clearable placeholder="请选择" class="filter-item">
          <el-option
            v-for="item in dict.market_place_id"
            :key="item.id"
            :label="item.label"
            :value="item.value" />
        </el-select>
        <label class="el-form-item-label">退货时间</label>
        <date-range-picker
          v-model="query.returnDate"
          start-placeholder="开始时间"
          end-placeholder="结束时间"
          class="date-item"
        />
        <rrOperation :crud="crud" />
      </div>
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="店铺ID">
            <el-input v-model="form.sellingPartnerId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="报告ID">
            <el-input v-model="form.reportId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="站点ID">
            <el-select v-model="form.marketplaceId" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.market_place_id"
                :key="item.id"
                :label="item.label"
                :value="item.value" />
            </el-select>
          </el-form-item>
          <el-form-item label="退货日期">
            <el-date-picker v-model="form.returnDate" type="datetime" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="订单ID">
            <el-input v-model="form.orderId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="商户订单ID">
            <el-input v-model="form.sku" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="调整ID">
            <el-input v-model="form.asin" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="运输ID">
            <el-input v-model="form.fnsku" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="商品名">
            <el-input v-model="form.productName" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="数量">
            <el-input v-model="form.quantity" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="物流中心ID">
            <el-input v-model="form.fulfillmentCenterId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="描述">
            <el-input v-model="form.detailedDisposition" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="理由">
            <el-input v-model="form.reason" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="状态">
            <el-input v-model="form.status" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="编号">
            <el-input v-model="form.licensePlateNumber" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="正文">
            <el-input v-model="form.customerComments" style="width: 370px;" />
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="text" @click="crud.cancelCU">取消</el-button>
          <el-button :loading="crud.status.cu === 2" type="primary" @click="crud.submitCU">确认</el-button>
        </div>
      </el-dialog>
      <!--表格渲染-->
      <el-table ref="table" v-loading="crud.loading" :data="crud.data" size="small" style="width: 100%;" @selection-change="crud.selectionChangeHandler">
        <el-table-column type="selection" width="55" />
<!--        <el-table-column prop="rowId" label="ID" />-->
        <el-table-column prop="sellingPartnerId" label="店铺ID" width="150" align="center" />
        <el-table-column prop="reportId" label="报告ID" width="150" align="center" />
        <el-table-column prop="marketplaceId" label="站点ID" width="150" align="center" >
          <template slot-scope="scope">
            {{ dict.label.market_place_id[scope.row.marketplaceId] }}
          </template>
        </el-table-column>
        <el-table-column prop="returnDate" label="退货日期" width="150" align="center" />
        <el-table-column prop="orderId" label="订单ID" width="150" align="center" />
        <el-table-column prop="sku" label="商户订单ID" width="150" align="center" />
        <el-table-column prop="asin" label="调整ID" width="150" align="center" />
        <el-table-column prop="fnsku" label="运输ID" width="150" align="center" />
        <el-table-column prop="productName" label="商品名" width="200" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="quantity" label="数量" width="150" align="center" />
        <el-table-column prop="fulfillmentCenterId" label="物流中心ID" width="150" align="center" />
        <el-table-column prop="detailedDisposition" label="描述" width="180" align="center" />
        <el-table-column prop="reason" label="理由" width="180" align="center" />
        <el-table-column prop="status" label="状态" width="180" align="center" />
        <el-table-column prop="licensePlateNumber" label="编号" width="150" align="center" />
        <el-table-column prop="customerComments" label="正文" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="rowCrtTs" label="创建日期" width="150" align="center" />
        <el-table-column prop="rowUptTs" label="更新时间" width="150" align="center" />
<!--        <el-table-column v-if="checkPer(['admin','dcAmazonReportReturn:edit','dcAmazonReportReturn:del'])" label="操作" width="150px" align="center">
          <template slot-scope="scope">
            <udOperation
              :data="scope.row"
              :permission="permission"
            />
          </template>
        </el-table-column>-->
      </el-table>
      <!--分页组件-->
      <pagination />
    </div>
  </div>
</template>

<script>
import crudDcAmazonReportReturn from '@/api/dcAmazonReportReturn'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'
import DateRangePicker from '@/components/DateRangePicker'

const defaultForm = { rowId: null, sellingPartnerId: null, reportId: null, marketplaceId: null, returnDate: null, orderId: null, sku: null, asin: null, fnsku: null, productName: null, quantity: null, fulfillmentCenterId: null, detailedDisposition: null, reason: null, status: null, licensePlateNumber: null, customerComments: null, rowCrtTs: null, rowUptTs: null, rowTp: null }
export default {
  name: 'DcAmazonReportReturn',
  components: { pagination, crudOperation, rrOperation, udOperation, DateRangePicker },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  dicts: ['market_place_id'],
  cruds() {
    return CRUD({ title: '亚马逊退货报告', url: 'api/dcAmazonReportReturn', idField: 'rowId', sort: 'rowId,desc', crudMethod: { ...crudDcAmazonReportReturn },
      optShow: {
        add: false,
        edit: false,
        del: false,
        download: true
      }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'dcAmazonReportReturn:add'],
        edit: ['admin', 'dcAmazonReportReturn:edit'],
        del: ['admin', 'dcAmazonReportReturn:del']
      },
      rules: {
      },
      queryTypeOptions: [
        { key: 'sellingPartnerId', display_name: '店铺ID' },
        { key: 'reportId', display_name: '报告ID' },
        { key: 'orderId', display_name: '订单ID' }
      ]
    }
  },
  methods: {
    // 钩子：在获取表格数据之前执行，false 则代表不获取数据
    [CRUD.HOOK.beforeRefresh]() {
      return true
    }
  }
}
</script>

<style scoped>

</style>
