<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
        <label class="el-form-item-label">店铺ID</label>
        <el-input v-model="query.sellingPartnerId" clearable placeholder="店铺ID" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">国家代码</label>
        <el-select v-model="query.countryCode" filterable clearable placeholder="请选择" class="filter-item">
          <el-option
            v-for="item in dict.country_code"
            :key="item.id"
            :label="item.label"
            :value="item.value" />
        </el-select>
        <label class="el-form-item-label">销售状态</label>
        <el-select v-model="query.salesStatus" filterable clearable placeholder="请选择" class="filter-item">
          <el-option
            v-for="item in dict.sales_status"
            :key="item.id"
            :label="item.label"
            :value="item.value" />
        </el-select>
        <br/>
        <label class="el-form-item-label">数据权限</label>
        <el-select v-model="query.dataAuthStatus" filterable clearable placeholder="请选择" class="filter-item">
          <el-option
            v-for="item in dict.data_auth_status"
            :key="item.id"
            :label="item.label"
            :value="item.value" />
        </el-select>
        <label class="el-form-item-label">更新时间</label>
        <date-range-picker
          v-model="query.rowCrtTs"
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
          <el-form-item label="店铺ID" prop="sellingPartnerId">
            <el-input v-model="form.sellingPartnerId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="国家代码" prop="countryCode">
            <el-select v-model="form.countryCode" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.country_code"
                :key="item.id"
                :label="item.label"
                :value="item.value" />
            </el-select>
          </el-form-item>
          <el-form-item label="销售状态">
            <el-select v-model="form.salesStatus" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.sales_status"
                :key="item.id"
                :label="item.label"
                :value="item.value" />
            </el-select>
          </el-form-item>
          <el-form-item label="店铺开始经营日期">
            <el-input v-model="form.startSalesDate" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="第一笔订单时间">
            <el-input v-model="form.firstOrderTime" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="数据权限">
            <el-select v-model="form.dataAuthStatus" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.data_auth_status"
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
        <el-table-column prop="sellingPartnerId" label="店铺ID" width="150" align="center" />
        <el-table-column prop="countryCode" label="国家代码" width="100" align="center">
          <template slot-scope="scope">
            {{ dict.label.country_code[scope.row.countryCode] }}
          </template>
        </el-table-column>
        <el-table-column prop="salesStatus" label="销售状态" width="100" align="center">
          <template slot-scope="scope">
            {{ dict.label.sales_status[scope.row.salesStatus] }}
          </template>
        </el-table-column>
<!--        <el-table-column prop="startSalesDate" label="店铺开始经营日期" width="150" align="center"/>-->
        <el-table-column prop="firstOrderTime" label="第一笔订单时间" width="150" align="center" />
        <el-table-column prop="dataAuthStatus" label="数据权限" width="100" align="center">
          <template slot-scope="scope">
            {{ dict.label.data_auth_status[scope.row.dataAuthStatus] }}
          </template>
        </el-table-column>
        <el-table-column prop="rowCrtTs" label="创建时间" width="150" align="center" />
        <el-table-column prop="rowUptTs" label="更新时间" width="150" align="center" />
        <el-table-column v-if="checkPer(['admin','dcAmazonSellerMarketPlace:edit','dcAmazonSellerMarketPlace:del'])" label="操作" width="150px" fixed="right" align="center">
          <template slot-scope="scope">
            <udOperation
              :data="scope.row"
              :permission="permission"
            />
          </template>
        </el-table-column>
      </el-table>
      <!--分页组件-->
      <pagination />
    </div>
  </div>
</template>

<script>
import crudDcAmazonSellerMarketPlace from '@/api/dcAmazonSellerMarketPlace'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'
import DateRangePicker from '@/components/DateRangePicker'

const defaultForm = { rowId: null, sellingPartnerId: null, countryCode: null, salesStatus: null, startSalesDate: null, firstOrderTime: null, dataAuthStatus: null, rowCrtTs: null, rowUptTs: null, rowTp: null }
export default {
  name: 'DcAmazonSellerMarketPlace',
  components: { pagination, crudOperation, rrOperation, udOperation, DateRangePicker },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  dicts: ['country_code', 'sales_status', 'data_auth_status'],
  cruds() {
    return CRUD(
      {
        title: '亚马逊店铺经营区域',
        url: 'api/dcAmazonSellerMarketPlace',
        idField: 'rowId',
        sort: 'rowId,desc', crudMethod: { ...crudDcAmazonSellerMarketPlace },
        optShow: {
          add: false,
          edit: true,
          del: true,
          download: true,
          reset: true
        }
      }
    )
  },
  data() {
    return {
      permission: {
        add: ['admin', 'dcAmazonSellerMarketPlace:add'],
        edit: ['admin', 'dcAmazonSellerMarketPlace:edit'],
        del: ['admin', 'dcAmazonSellerMarketPlace:del']
      },
      rules: {
        sellingPartnerId: [
          { required: true, message: '店铺ID不能为空', trigger: 'blur' }
        ],
        countryCode: [
          { required: true, message: '国家代码不能为空', trigger: 'blur' }
        ]
      },
      queryTypeOptions: [
        { key: 'sellingPartnerId', display_name: '店铺ID' },
        { key: 'countryCode', display_name: '国家代码' },
        { key: 'salesStatus', display_name: '销售状态' },
        { key: 'dataAuthStatus', display_name: '数据权限' }
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
