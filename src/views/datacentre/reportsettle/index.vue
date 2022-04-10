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
        <label class="el-form-item-label">销售站点</label>
        <el-select v-model="query.marketplaceName" filterable clearable placeholder="请选择" class="filter-item">
          <el-option
            v-for="item in dict.marketplace_name"
            :key="item.id"
            :label="item.label"
            :value="item.value" />
        </el-select>
        <label class="el-form-item-label">结算开始时间</label>
        <date-range-picker
          v-model="query.settlementStartDate"
          start-placeholder="开始时间"
          end-placeholder="结束时间"
          class="date-item"
        />
        <label class="el-form-item-label">结算截止时间</label>
        <date-range-picker
          v-model="query.settlementEndDate"
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
          <el-form-item label="结算ID">
            <el-input v-model="form.settlementId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="结算开始时间">
            <el-date-picker v-model="form.settlementStartDate" type="datetime" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="结算截止时间">
            <el-date-picker v-model="form.settlementEndDate" type="datetime" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="存款时间">
            <el-input v-model="form.depositDate" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="总金额">
            <el-input v-model="form.totalAmount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="币种">
            <el-input v-model="form.currency" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="交易类型">
            <el-input v-model="form.transactionType" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="订单ID">
            <el-input v-model="form.orderId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="商户订单ID">
            <el-input v-model="form.merchantOrderId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="调整ID">
            <el-input v-model="form.adjustmentId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="运输ID">
            <el-input v-model="form.shipmentId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="商城名">
            <el-select v-model="form.marketplaceName" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.marketplace_name"
                :key="item.id"
                :label="item.label"
                :value="item.value" />
            </el-select>
          </el-form-item>
          <el-form-item label="运费类型">
            <el-input v-model="form.shipmentFeeType" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="运费金额">
            <el-input v-model="form.shipmentFeeAmount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="订单费用类型">
            <el-input v-model="form.orderFeeType" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="订单费用金额">
            <el-input v-model="form.orderFeeAmount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="服务ID">
            <el-input v-model="form.fulfillmentId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="通告时间">
            <el-input v-model="form.postedDate" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="订单明细代码">
            <el-input v-model="form.orderItemCode" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="商户订单明细ID">
            <el-input v-model="form.merchantOrderItemId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="商户订单明细调整ID">
            <el-input v-model="form.merchantAdjustmentItemId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="SKU">
            <el-input v-model="form.sku" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="购买数量">
            <el-input v-model="form.quantityPurchased" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="价格类型">
            <el-input v-model="form.priceType" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="价格">
            <el-input v-model="form.priceAmount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="明细关联费用类型">
            <el-input v-model="form.itemRelatedFeeType" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="明细关联费用金额">
            <el-input v-model="form.itemRelatedFeeAmount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="misc费用金额">
            <el-input v-model="form.miscFeeAmount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="其它费用金额">
            <el-input v-model="form.otherFeeAmount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="其它费用原因描述">
            <el-input v-model="form.otherFeeReasonDescription" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="促销ID">
            <el-input v-model="form.promotionId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="促销类型">
            <el-input v-model="form.promotionType" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="促销金额">
            <el-input v-model="form.promotionAmount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="直接支付类型">
            <el-input v-model="form.directPaymentType" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="直接支付金额">
            <el-input v-model="form.directPaymentAmount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="其它金额">
            <el-input v-model="form.otherAmount" style="width: 370px;" />
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
        <el-table-column prop="reportId" label="报告ID" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="settlementId" label="结算ID" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="settlementStartDate" label="结算开始时间" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="settlementEndDate" label="结算截止时间" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="depositDate" label="存款时间" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="totalAmount" label="总金额" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="currency" label="币种" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="transactionType" label="交易类型" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="orderId" label="订单ID" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="merchantOrderId" label="商户订单ID" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="adjustmentId" label="调整ID" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="shipmentId" label="运输ID" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="marketplaceName" label="商城名" width="150" align="center" show-overflow-tooltip="true">
          <template slot-scope="scope">
            {{ dict.label.marketplace_name[scope.row.marketplaceName] }}
          </template>
        </el-table-column>
        <el-table-column prop="shipmentFeeType" label="运费类型" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="shipmentFeeAmount" label="运费金额" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="orderFeeType" label="订单费用类型" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="orderFeeAmount" label="订单费用金额" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="fulfillmentId" label="服务ID" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="postedDate" label="通告时间" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="orderItemCode" label="订单明细代码" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="merchantOrderItemId" label="商户订单明细ID" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="merchantAdjustmentItemId" label="商户订单明细调整ID" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="sku" label="SKU" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="quantityPurchased" label="购买数量" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="priceType" label="价格类型" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="priceAmount" label="价格" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="itemRelatedFeeType" label="明细关联费用类型" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="itemRelatedFeeAmount" label="明细关联费用金额" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="miscFeeAmount" label="misc费用金额" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="otherFeeAmount" label="其它费用金额" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="otherFeeReasonDescription" label="其它费用原因描述" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="promotionId" label="促销ID" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="promotionType" label="促销类型" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="promotionAmount" label="促销金额" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="directPaymentType" label="直接支付类型" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="directPaymentAmount" label="直接支付金额" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="otherAmount" label="其它金额" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="rowCrtTs" label="创建日期" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="rowUptTs" label="更新时间" width="150" align="center" show-overflow-tooltip="true" />
<!--        <el-table-column v-if="checkPer(['admin','dcAmazonReportSettle:edit','dcAmazonReportSettle:del'])" label="操作" width="150px" align="center">-->
<!--          <template slot-scope="scope">-->
<!--            <udOperation-->
<!--              :data="scope.row"-->
<!--              :permission="permission"-->
<!--            />-->
<!--          </template>-->
<!--        </el-table-column>-->
      </el-table>
      <!--分页组件-->
      <pagination />
    </div>
  </div>
</template>

<script>
import crudDcAmazonReportSettle from '@/api/dcAmazonReportSettle'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'
import DateRangePicker from '@/components/DateRangePicker'

const defaultForm = { rowId: null, sellingPartnerId: null, reportId: null, settlementId: null, settlementStartDate: null, settlementEndDate: null, depositDate: null, totalAmount: null, currency: null, transactionType: null, orderId: null, merchantOrderId: null, adjustmentId: null, shipmentId: null, marketplaceName: null, shipmentFeeType: null, shipmentFeeAmount: null, orderFeeType: null, orderFeeAmount: null, fulfillmentId: null, postedDate: null, orderItemCode: null, merchantOrderItemId: null, merchantAdjustmentItemId: null, sku: null, quantityPurchased: null, priceType: null, priceAmount: null, itemRelatedFeeType: null, itemRelatedFeeAmount: null, miscFeeAmount: null, otherFeeAmount: null, otherFeeReasonDescription: null, promotionId: null, promotionType: null, promotionAmount: null, directPaymentType: null, directPaymentAmount: null, otherAmount: null, rowCrtTs: null, rowUptTs: null, rowTp: null }
export default {
  name: 'DcAmazonReportSettle',
  components: { pagination, crudOperation, rrOperation, udOperation, DateRangePicker },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  dicts: ['marketplace_name'],
  cruds() {
    return CRUD({ title: '亚马逊结算报告', url: 'api/dcAmazonReportSettle', idField: 'rowId', sort: 'rowId,desc', crudMethod: { ...crudDcAmazonReportSettle },
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
        add: ['admin', 'dcAmazonReportSettle:add'],
        edit: ['admin', 'dcAmazonReportSettle:edit'],
        del: ['admin', 'dcAmazonReportSettle:del']
      },
      rules: {
      },
      queryTypeOptions: [
        { key: 'sellingPartnerId', display_name: '店铺ID' },
        { key: 'reportId', display_name: '报告ID' },
        { key: 'orderId', display_name: '订单ID' },
        { key: 'marketplaceName', display_name: '商城名' }
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
