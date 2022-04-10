<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
        <label class="el-form-item-label">店铺id</label>
        <el-input v-model="query.sellingPartnerId" clearable placeholder="店铺id" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">亚马逊订单编号</label>
        <el-input v-model="query.amazonOrderId" clearable placeholder="亚马逊订单编号" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">订单创建时间</label>
        <date-range-picker
          v-model="query.purchaseDateTime"
          start-placeholder="purchaseDateTimeStart"
          end-placeholder="purchaseDateTimeStart"
          class="date-item"
        />
        <rrOperation :crud="crud" />
      </div>
      <!--如果想在工具栏加入更多按钮，可以使用插槽方式， slot = 'left' or 'right'-->
      <crudOperation :permission="permission" />
      <!--表单组件-->
      <el-dialog :close-on-click-modal="false" :before-close="crud.cancelCU" :visible.sync="crud.status.cu > 0" :title="crud.status.title" width="500px">
        <el-form ref="form" :model="form" :rules="rules" size="small" label-width="80px">
          <el-form-item label="店铺id">
            <el-input v-model="form.sellingPartnerId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="亚马逊订单编号">
            <el-input v-model="form.amazonOrderId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="订单创建日期">
            <el-date-picker v-model="form.purchaseDateTime" type="datetime" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="ASIN">
            <el-input v-model="form.asin" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="SKU">
            <el-input v-model="form.sellerSku" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="订单明细ID">
            <el-input v-model="form.orderItemId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="标题">
            <el-input v-model="form.title" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="订单商品数量">
            <el-input v-model="form.quantityOrdered" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="发货商品数量">
            <el-input v-model="form.quantityShipped" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="订单ASIN数量">
            <el-input v-model="form.numberOfItems" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="积分数量">
            <el-input v-model="form.pointsNumber" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="积分种类">
            <el-input v-model="form.pointsMonetaryValueCurrencyCode" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="积分金额">
            <el-input v-model="form.pointsMonetaryValueAmount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="销售价格币种">
            <el-input v-model="form.itemPriceCurrencyCode" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="销售价格金额">
            <el-input v-model="form.itemPriceAmount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="运输价格币种">
            <el-input v-model="form.shippingPriceCurrencyCode" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="运输价格金额">
            <el-input v-model="form.shippingPriceAmount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="商品税币种">
            <el-input v-model="form.itemtaxCurrencyCode" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="商品税金额">
            <el-input v-model="form.itemtaxAmount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="运费税币种">
            <el-input v-model="form.shippingTaxCurrencyCode" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="运费税金额">
            <el-input v-model="form.shippingTaxAmount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="运费折扣币种">
            <el-input v-model="form.shippingDiscountCurrencyCode" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="运费折扣金额">
            <el-input v-model="form.shippingDiscountAmount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="运费折扣税币种">
            <el-input v-model="form.shippingDiscountTaxCurrencyCode" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="运费折扣税金额">
            <el-input v-model="form.shippingDiscountTaxAmount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="促销折扣币种">
            <el-input v-model="form.promotionDiscountCurrencyCode" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="促销折扣金额">
            <el-input v-model="form.promotionDiscountAmount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="促销折扣税币种">
            <el-input v-model="form.promotionDiscountTaxCurrencyCode" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="促销折扣税金额">
            <el-input v-model="form.promotionDiscountTaxAmount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="促销id列表">
            <el-input v-model="form.promotionids" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="货到付款币种">
            <el-input v-model="form.codfeeCurrencyCode" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="货到付款金额">
            <el-input v-model="form.codfeeAmount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="货到付款折扣币种">
            <el-input v-model="form.codfeeDiscountCurrencyCode" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="货到付款折扣金额">
            <el-input v-model="form.codfeeDiscountAmount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="是否是礼物">
            <el-input v-model="form.isGift" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="商品状况描述">
            <el-input v-model="form.conditionNote" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="商品状况ID">
            <el-input v-model="form.conditionId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="商品状况子ID">
            <el-input v-model="form.conditionSubTypeId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="预定交付开始日期">
            <el-input v-model="form.scheduledDeliveryStartDate" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="预定交付截止日期">
            <el-input v-model="form.scheduledDeliveryEndDate" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="特殊价格标识">
            <el-input v-model="form.priceDesignation" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="税收征收模式">
            <el-input v-model="form.taxCollectionModel" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="代扣代缴税款方">
            <el-input v-model="form.taxCollectionResponsibleParty" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="是否产品类型具有序列号">
            <el-input v-model="form.serialNumberRequired" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="需要制定透明的法规">
            <el-input v-model="form.isTransparency" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="IOSS号码">
            <el-input v-model="form.iossNumber" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="连锁商店标识符">
            <el-input v-model="form.storeChainStoreId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="被视为转售者的类别">
            <el-input v-model="form.deemedResellerCategory" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="AmazonCustom数据的压缩文件">
            <el-input v-model="form.customizedUrl" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="礼品包装价格币种">
            <el-input v-model="form.giftWrapPriceCurrencyCode" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="礼品包装价格金额">
            <el-input v-model="form.giftWrapPriceAmount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="礼品包装价格税费币种">
            <el-input v-model="form.giftWrapTaxCurrencyCode" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="礼品包装价格税费金额">
            <el-input v-model="form.giftWrapTaxAmount" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="礼品信息">
            <el-input v-model="form.giftMessageText" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="礼品包装级别">
            <el-input v-model="form.giftWrapLevel" style="width: 370px;" />
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
<!--        <el-table-column prop="rowId" label="id" />-->
        <el-table-column prop="sellingPartnerId" label="店铺id" width="150" align="center" />
        <el-table-column prop="amazonOrderId" label="亚马逊订单编号" width="150" align="center" />
        <el-table-column prop="purchaseDateTime" label="订单创建日期" width="150" align="center" />
        <el-table-column prop="asin" label="ASIN" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="sellerSku" label="SKU" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="orderItemId" label="订单明细ID" width="150" align="center" />
        <el-table-column prop="title" label="标题" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="quantityOrdered" label="订单商品数量" width="150" align="center" />
        <el-table-column prop="quantityShipped" label="发货商品数量" width="150" align="center" />
        <el-table-column prop="numberOfItems" label="订单ASIN数量" width="150" align="center" />
        <el-table-column prop="pointsNumber" label="积分数量" width="150" align="center" />
        <el-table-column prop="pointsMonetaryValueCurrencyCode" label="积分种类" width="150" align="center" />
        <el-table-column prop="pointsMonetaryValueAmount" label="积分金额" width="150" align="center" />
        <el-table-column prop="itemPriceCurrencyCode" label="销售价格币种" width="150" align="center" />
        <el-table-column prop="itemPriceAmount" label="销售价格金额" width="150" align="center" />
        <el-table-column prop="shippingPriceCurrencyCode" label="运输价格币种" width="150" align="center" />
        <el-table-column prop="shippingPriceAmount" label="运输价格金额" width="150" align="center" />
        <el-table-column prop="itemtaxCurrencyCode" label="商品税币种" width="150" align="center" />
        <el-table-column prop="itemtaxAmount" label="商品税金额" width="150" align="center" />
        <el-table-column prop="shippingTaxCurrencyCode" label="运费税币种" width="150" align="center" />
        <el-table-column prop="shippingTaxAmount" label="运费税金额" width="150" align="center" />
        <el-table-column prop="shippingDiscountCurrencyCode" label="运费折扣币种" width="150" align="center" />
        <el-table-column prop="shippingDiscountAmount" label="运费折扣金额" width="150" align="center" />
        <el-table-column prop="shippingDiscountTaxCurrencyCode" label="运费折扣税币种" width="150" align="center" />
        <el-table-column prop="shippingDiscountTaxAmount" label="运费折扣税金额" width="150" align="center" />
        <el-table-column prop="promotionDiscountCurrencyCode" label="促销折扣币种" width="150" align="center" />
        <el-table-column prop="promotionDiscountAmount" label="促销折扣金额" width="150" align="center" />
        <el-table-column prop="promotionDiscountTaxCurrencyCode" label="促销折扣税币种" width="150" align="center" />
        <el-table-column prop="promotionDiscountTaxAmount" label="促销折扣税金额" width="150" align="center" />
        <el-table-column prop="promotionids" label="促销id列表" width="150" align="center" />
        <el-table-column prop="codfeeCurrencyCode" label="货到付款币种" width="150" align="center" />
        <el-table-column prop="codfeeAmount" label="货到付款金额" width="150" align="center" />
        <el-table-column prop="codfeeDiscountCurrencyCode" label="货到付款折扣币种" width="150" align="center" />
        <el-table-column prop="codfeeDiscountAmount" label="货到付款折扣金额" width="150" align="center" />
        <el-table-column prop="isGift" label="是否是礼物" width="150" align="center" />
        <el-table-column prop="conditionNote" label="商品状况描述" width="150" align="center" />
        <el-table-column prop="conditionId" label="商品状况ID" width="150" align="center" />
        <el-table-column prop="conditionSubTypeId" label="商品状况子ID" width="150" align="center" />
        <el-table-column prop="scheduledDeliveryStartDate" label="预定交付开始日期" width="150" align="center" />
        <el-table-column prop="scheduledDeliveryEndDate" label="预定交付截止日期" width="150" align="center" />
        <el-table-column prop="priceDesignation" label="特殊价格标识" width="150" align="center" />
        <el-table-column prop="taxCollectionModel" label="税收征收模式" width="150" align="center" />
        <el-table-column prop="taxCollectionResponsibleParty" label="代扣代缴税款方" width="150" align="center" />
        <el-table-column prop="serialNumberRequired" label="是否产品类型具有序列号" width="150" align="center" />
        <el-table-column prop="isTransparency" label="需要制定透明的法规" width="150" align="center" />
        <el-table-column prop="iossNumber" label="IOSS号码" width="150" align="center" />
        <el-table-column prop="storeChainStoreId" label="连锁商店标识符" width="150" align="center" />
        <el-table-column prop="deemedResellerCategory" label="被视为转售者的类别" width="150" align="center" />
        <el-table-column prop="customizedUrl" label="AmazonCustom数据的压缩文件" width="150" align="center" />
        <el-table-column prop="giftWrapPriceCurrencyCode" label="礼品包装价格币种" width="150" align="center" />
        <el-table-column prop="giftWrapPriceAmount" label="礼品包装价格金额" width="150" align="center" />
        <el-table-column prop="giftWrapTaxCurrencyCode" label="礼品包装价格税费币种" width="150" align="center" />
        <el-table-column prop="giftWrapTaxAmount" label="礼品包装价格税费金额" width="150" align="center" />
        <el-table-column prop="giftMessageText" label="礼品信息" width="150" align="center" />
        <el-table-column prop="giftWrapLevel" label="礼品包装级别" width="150" align="center" />
        <el-table-column prop="rowCrtTs" label="记录创建日期" width="150" align="center" />
        <el-table-column prop="rowUptTs" label="记录更新时间" width="150" align="center" />
<!--        <el-table-column v-if="checkPer(['admin','dcAmazonOrderItem:edit','dcAmazonOrderItem:del'])" label="操作" width="150px" align="center">
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
import crudDcAmazonOrderItem from '@/api/dcAmazonOrderItem'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'
import DateRangePicker from '@/components/DateRangePicker'

const defaultForm = { rowId: null, sellingPartnerId: null, amazonOrderId: null, purchaseDateTime: null, asin: null, sellerSku: null, orderItemId: null, title: null, quantityOrdered: null, quantityShipped: null, numberOfItems: null, pointsNumber: null, pointsMonetaryValueCurrencyCode: null, pointsMonetaryValueAmount: null, itemPriceCurrencyCode: null, itemPriceAmount: null, shippingPriceCurrencyCode: null, shippingPriceAmount: null, itemtaxCurrencyCode: null, itemtaxAmount: null, shippingTaxCurrencyCode: null, shippingTaxAmount: null, shippingDiscountCurrencyCode: null, shippingDiscountAmount: null, shippingDiscountTaxCurrencyCode: null, shippingDiscountTaxAmount: null, promotionDiscountCurrencyCode: null, promotionDiscountAmount: null, promotionDiscountTaxCurrencyCode: null, promotionDiscountTaxAmount: null, promotionids: null, codfeeCurrencyCode: null, codfeeAmount: null, codfeeDiscountCurrencyCode: null, codfeeDiscountAmount: null, isGift: null, conditionNote: null, conditionId: null, conditionSubTypeId: null, scheduledDeliveryStartDate: null, scheduledDeliveryEndDate: null, priceDesignation: null, taxCollectionModel: null, taxCollectionResponsibleParty: null, serialNumberRequired: null, isTransparency: null, iossNumber: null, storeChainStoreId: null, deemedResellerCategory: null, customizedUrl: null, giftWrapPriceCurrencyCode: null, giftWrapPriceAmount: null, giftWrapTaxCurrencyCode: null, giftWrapTaxAmount: null, giftMessageText: null, giftWrapLevel: null, rowCrtTs: null, rowUptTs: null, rowTp: null }
export default {
  name: 'DcAmazonOrderItem',
  components: { pagination, crudOperation, rrOperation, udOperation, DateRangePicker },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  cruds() {
    return CRUD({ title: '亚马逊订单明细', url: 'api/dcAmazonOrderItem', idField: 'rowId', sort: 'rowId,desc', crudMethod: { ...crudDcAmazonOrderItem }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'dcAmazonOrderItem:add'],
        edit: ['admin', 'dcAmazonOrderItem:edit'],
        del: ['admin', 'dcAmazonOrderItem:del']
      },
      rules: {
      },
      queryTypeOptions: [
        { key: 'sellingPartnerId', display_name: '店铺id' },
        { key: 'amazonOrderId', display_name: '亚马逊订单编号' }
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
