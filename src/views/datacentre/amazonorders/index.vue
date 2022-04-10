<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
        <label class="el-form-item-label">店铺ID</label>
        <el-input v-model="query.sellingPartnerId" clearable placeholder="店铺ID" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">亚马逊订单编号</label>
        <el-input v-model="query.amazonOrderId" clearable placeholder="亚马逊订单编号" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">商城编码</label>
        <el-select v-model="query.marketPlaceId" filterable clearable placeholder="请选择" class="filter-item">
          <el-option
            v-for="item in dict.market_place_id"
            :key="item.id"
            :label="item.label"
            :value="item.value" />
        </el-select>
        <br />
        <label class="el-form-item-label">订单创建时间</label>
        <date-range-picker
          v-model="query.purchaseDateTime"
          start-placeholder="开始时间"
          end-placeholder="结束时间"
          class="date-item"
        />
        <label class="el-form-item-label">订单最后更新时间</label>
        <date-range-picker
          v-model="query.lastUpdateDate"
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
          <el-form-item label="商城编码">
            <el-select v-model="form.marketPlaceId" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.market_place_id"
                :key="item.id"
                :label="item.label"
                :value="item.value" />
            </el-select>
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
        <el-table-column prop="marketPlaceId" label="商城" width="60" align="center" >
          <template slot-scope="scope">
            {{ dict.label.market_place_id[scope.row.marketPlaceId] }}
          </template>
        </el-table-column>
        <el-table-column prop="sellingPartnerId" label="店铺ID" width="150" align="center" />
        <el-table-column prop="amazonOrderId" label="亚马逊订单编号" width="150" align="center" />
        <el-table-column prop="sellerOrderId" label="卖家订单编号" width="150" align="center" />
<!--        <el-table-column prop="purchaseDate" label="创建订单时间" />-->
        <el-table-column prop="purchaseDateTime" label="订单创建时间" width="150" align="center" />
        <el-table-column prop="lastUpdateDate" label="订单最后更新时间" width="150" align="center" />
        <el-table-column prop="orderStatus" label="当前状态" width="100" align="center" />
        <el-table-column prop="fulfillmentChannel" label="配送方式" width="100" align="center" />
        <el-table-column prop="salesChannel" label="销售渠道" width="120" align="center" />
        <el-table-column prop="orderTotalAmount" label="总费用" width="150" align="center" />
        <el-table-column prop="orderTotalCurrencyCode" label="交易币种" width="150" align="center" />
        <el-table-column prop="numberOfItemsShipped" label="已配送商品数量" width="150" align="center" />
        <el-table-column prop="numberOfItemsUnshipped" label="未配送商品数量" width="150" align="center" />

<!--        <el-table-column prop="orderChannel" label="订单渠道" width="100" align="center" />-->
<!--        <el-table-column prop="shipServiceLeavel" label="服务水平" width="150" align="center" />-->
<!--        <el-table-column prop="paymentExecutionDetail" label="货到付款订单付款方式" width="150" align="center" />-->
<!--        <el-table-column prop="paymentMethod" label="主要付款方式" width="150" align="center" />-->
<!--        <el-table-column prop="paymentMethodDetails" label="订单支付方式列表" width="150" align="center" />-->
<!--        <el-table-column prop="shipmentServiceLevelCategory" label="配送服务级别" width="150" align="center" />-->
<!--        <el-table-column prop="easyShipShipmentStatus" label="EasyShip订单状态" width="150" align="center" />-->
<!--        <el-table-column prop="cbaDisplayableShippingLabel" label="卖家自定义的配送方式" width="150" align="center" />-->
<!--        <el-table-column prop="orderType" label="订单类型" width="150" align="center" />-->
<!--        <el-table-column prop="earliestShipDate" label="承诺发货时间" width="150" align="center" />-->
<!--        <el-table-column prop="latestShipDate" label="承诺最晚发货时间" width="150" align="center" />-->
<!--        <el-table-column prop="earliestDeliveryDate" label="承诺送达时间" width="150" align="center" />-->
<!--        <el-table-column prop="latestDeliveryDate" label="承诺最晚送达时间" width="150" align="center" />-->
<!--        <el-table-column prop="isBusinessOrder" label="是否自营订单" width="150" align="center" />-->
<!--        <el-table-column prop="isPrime" label="是否prime订单" width="150" align="center" />-->
<!--        <el-table-column prop="isPremiumOrder" label="是否premium订单" width="150" align="center" />-->
<!--        <el-table-column prop="isGlobalExpressEnabled" label="是否GlobalExpress订单" width="150" align="center" />-->
<!--        <el-table-column prop="replacedOrderId" label="被替换订单ID" width="150" align="center" />-->
<!--        <el-table-column prop="isReplacementOrder" label="是否为替换订单" width="150" align="center" />-->
<!--        <el-table-column prop="promiseResponseDueDate" label="采购预估装运日期" width="150" align="center" />-->
<!--        <el-table-column prop="isEstimatedShipDateSet" label="采购预估发货日期" width="150" align="center" />-->
<!--        <el-table-column prop="isSoldByAb" label="是否由ABEU购买和转售" width="150" align="center" />-->
<!--        <el-table-column prop="defaultShipFromLocationAddressName" label="默认发货姓名" width="150" align="center" />-->
<!--        <el-table-column prop="defaultShipFromLocationAddressAddressline1" label="默认发货街道地址1" width="150" align="center" />-->
<!--        <el-table-column prop="defaultShipFromLocationAddressAddressline2" label="默认发货街道地址2" width="150" align="center" />-->
<!--        <el-table-column prop="defaultShipFromLocationAddressAddressline3" label="默认发货街道地址3" width="150" align="center" />-->
<!--        <el-table-column prop="defaultShipFromLocationAddressCity" label="默认发货城市" width="150" align="center" />-->
<!--        <el-table-column prop="defaultShipFromLocationAddressCounty" label="默认发货国家" width="150" align="center" />-->
<!--        <el-table-column prop="defaultShipFromLocationAddressDistrict" label="默认发货区" width="150" align="center" />-->
<!--        <el-table-column prop="defaultShipFromLocationAddressStateOrRegion" label="默认发货州或地区" width="150" align="center" />-->
<!--        <el-table-column prop="defaultShipFromLocationAddressMunicipality" label="默认发货直辖市" width="150" align="center" />-->
<!--        <el-table-column prop="defaultShipFromLocationAddressPostalCode" label="默认发货邮政编码" width="150" align="center" />-->
<!--        <el-table-column prop="defaultShipFromLocationAddressCountryCode" label="默认发货国家代码" width="150" align="center" />-->
<!--        <el-table-column prop="defaultShipFromLocationAddressPhone" label="默认发货电话号码" width="150" align="center" />-->
<!--        <el-table-column prop="defaultShipFromLocationAddressAddressType" label="默认发货地址类型" width="150" align="center" />-->
<!--        <el-table-column prop="buyerInvoicePreference" label="买方发票" width="150" align="center" />-->
<!--        <el-table-column prop="buyerTaxInformationLegalCompanyName" label="商业发票公司名称" width="150" align="center" />-->
<!--        <el-table-column prop="buyerTaxInformationBusinessAddress" label="商业发票地址" width="150" align="center" />-->
<!--        <el-table-column prop="buyerTaxInformationTaxRegistrationId" label="商业发票税务登记证" width="150" align="center" />-->
<!--        <el-table-column prop="buyerTaxInformationTaxOffice" label="商业发票税务办公室" width="150" align="center" />-->
<!--        <el-table-column prop="fulfillmentInstructionSupplySourceId" label="执行指令供应源id" width="150" align="center" />-->
<!--        <el-table-column prop="isIspu" label="是否自提" width="150" align="center" />-->
<!--        <el-table-column prop="marketplaceTaxInfo" label="税务分类列表" width="150" align="center" />-->
<!--        <el-table-column prop="sellerDisplayName" label="卖家显示名称" width="150" align="center" />-->
<!--        <el-table-column prop="shippingAddressName" label="订单送货姓名" width="150" align="center" />-->
<!--        <el-table-column prop="shippingAddressAddressline1" label="订单送货街道地址1" width="150" align="center" />-->
<!--        <el-table-column prop="shippingAddressAddressline2" label="订单送货街道地址2" width="150" align="center" />-->
<!--        <el-table-column prop="shippingAddressAddressline3" label="订单送货街道地址3" width="150" align="center" />-->
<!--        <el-table-column prop="shippingAddressCity" label="订单送货城市" width="150" align="center" />-->
<!--        <el-table-column prop="shippingAddressCounty" label="订单送货国家" width="150" align="center" />-->
<!--        <el-table-column prop="shippingAddressDistrict" label="订单送货区" width="150" align="center" />-->
<!--        <el-table-column prop="shippingAddressStateOrRegion" label="订单送货州或地区" width="150" align="center" />-->
<!--        <el-table-column prop="shippingAddressMunicipality" label="订单送货直辖市" width="150" align="center" />-->
<!--        <el-table-column prop="shippingAddressPostalCode" label="订单送货邮政编码" width="150" align="center" />-->
<!--        <el-table-column prop="shippingAddressCountryCode" label="订单送货国家代码" width="150" align="center" />-->
<!--        <el-table-column prop="shippingAddressPhone" label="订单送货电话号码" width="150" align="center" />-->
<!--        <el-table-column prop="shippingAddressAddressType" label="订单送货地址类型" width="150" align="center" />-->
<!--        <el-table-column prop="buyerInfoEmail" label="买家匿名邮箱" width="150" align="center" />-->
<!--        <el-table-column prop="buyerInfoName" label="买家名字" width="150" align="center" />-->
<!--        <el-table-column prop="buyerInfoCounty" label="买家县郡" width="150" align="center" />-->
<!--        <el-table-column prop="buyerInfoTaxInfoCompanyLegalName" label="买家税务信息-公司名称" width="150" align="center" />-->
<!--        <el-table-column prop="buyerInfoTaxInfoTaxingRegion" label="买家税务信息-征税的国家或地区" width="150" align="center" />-->
<!--        <el-table-column prop="buyerInfoTaxInfoTaxClassifications" label="买家税务信息" width="150" align="center" />-->
<!--        <el-table-column prop="buyerInfoPurchaseOrderNumber" label="买家采购订单PO号" width="150" align="center" />-->
<!--        <el-table-column prop="automatedShippingSettingsHas" label="是否有自动发货设置" width="150" align="center" />-->
<!--        <el-table-column prop="automatedShippingSettingsCarrier" label="SSA订单自动生成承运人" width="150" align="center" />-->
<!--        <el-table-column prop="automatedShippingSettingsShipMethod" label="SSA订单自动生成发货方法" width="150" align="center" />-->
        <el-table-column prop="rowCrtTs" label="记录创建日期" width="150" align="center" />
        <el-table-column prop="rowUptTs" label="记录更新时间" width="150" align="center" />
<!--        <el-table-column v-if="checkPer(['admin','dcAmazonOrder:edit','dcAmazonOrder:del'])" label="操作" width="150px" align="center">-->
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
import crudDcAmazonOrder from '@/api/dcAmazonOrder'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'
import DateRangePicker from '@/components/DateRangePicker'

const defaultForm = { rowId: null, purchaseDateTime: null, sellingPartnerId: null, amazonOrderId: null, sellerOrderId: null, purchaseDate: null, lastUpdateDate: null, orderStatus: null, fulfillmentChannel: null, salesChannel: null, orderChannel: null, shipServiceLeavel: null, orderTotalAmount: null, orderTotalCurrencyCode: null, numberOfItemsShipped: null, numberOfItemsUnshipped: null, paymentExecutionDetail: null, paymentMethod: null, paymentMethodDetails: null, marketPlaceId: null, shipmentServiceLevelCategory: null, easyShipShipmentStatus: null, cbaDisplayableShippingLabel: null, orderType: null, earliestShipDate: null, latestShipDate: null, earliestDeliveryDate: null, latestDeliveryDate: null, isBusinessOrder: null, isPrime: null, isPremiumOrder: null, isGlobalExpressEnabled: null, replacedOrderId: null, isReplacementOrder: null, promiseResponseDueDate: null, isEstimatedShipDateSet: null, isSoldByAb: null, defaultShipFromLocationAddressName: null, defaultShipFromLocationAddressAddressline1: null, defaultShipFromLocationAddressAddressline2: null, defaultShipFromLocationAddressAddressline3: null, defaultShipFromLocationAddressCity: null, defaultShipFromLocationAddressCounty: null, defaultShipFromLocationAddressDistrict: null, defaultShipFromLocationAddressStateOrRegion: null, defaultShipFromLocationAddressMunicipality: null, defaultShipFromLocationAddressPostalCode: null, defaultShipFromLocationAddressCountryCode: null, defaultShipFromLocationAddressPhone: null, defaultShipFromLocationAddressAddressType: null, buyerInvoicePreference: null, buyerTaxInformationLegalCompanyName: null, buyerTaxInformationBusinessAddress: null, buyerTaxInformationTaxRegistrationId: null, buyerTaxInformationTaxOffice: null, fulfillmentInstructionSupplySourceId: null, isIspu: null, marketplaceTaxInfo: null, sellerDisplayName: null, shippingAddressName: null, shippingAddressAddressline1: null, shippingAddressAddressline2: null, shippingAddressAddressline3: null, shippingAddressCity: null, shippingAddressCounty: null, shippingAddressDistrict: null, shippingAddressStateOrRegion: null, shippingAddressMunicipality: null, shippingAddressPostalCode: null, shippingAddressCountryCode: null, shippingAddressPhone: null, shippingAddressAddressType: null, buyerInfoEmail: null, buyerInfoName: null, buyerInfoCounty: null, buyerInfoTaxInfoCompanyLegalName: null, buyerInfoTaxInfoTaxingRegion: null, buyerInfoTaxInfoTaxClassifications: null, buyerInfoPurchaseOrderNumber: null, automatedShippingSettingsHas: null, automatedShippingSettingsCarrier: null, automatedShippingSettingsShipMethod: null, rowCrtTs: null, rowUptTs: null, rowTp: null }
export default {
  name: 'DcAmazonOrder',
  components: { pagination, crudOperation, rrOperation, udOperation, DateRangePicker },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  dicts: ['market_place_id'],
  cruds() {
    return CRUD({ title: '亚马逊订单数据', url: 'api/dcAmazonOrder', idField: 'purchaseDateTime', sort: 'purchaseDateTime,desc', crudMethod: { ...crudDcAmazonOrder },
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
        add: ['admin', 'dcAmazonOrder:add'],
        edit: ['admin', 'dcAmazonOrder:edit'],
        del: ['admin', 'dcAmazonOrder:del']
      },
      rules: {
      },
      queryTypeOptions: [
        { key: 'sellingPartnerId', display_name: '店铺ID' },
        { key: 'amazonOrderId', display_name: '亚马逊订单编号' },
        { key: 'marketPlaceId', display_name: '商城编码' }
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
