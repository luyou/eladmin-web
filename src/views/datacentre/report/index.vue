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
        <label class="el-form-item-label">商城代码</label>
        <el-select v-model="query.marketplaceId" filterable clearable placeholder="请选择" class="filter-item">
          <el-option
            v-for="item in dict.market_place_id"
            :key="item.id"
            :label="item.label"
            :value="item.value" />
        </el-select>
        <br />
        <label class="el-form-item-label">报告状态</label>
        <el-select v-model="query.reportStatus" filterable clearable placeholder="请选择" class="filter-item">
          <el-option
            v-for="item in dict.report_status"
            :key="item.id"
            :label="item.label"
            :value="item.value" />
        </el-select>
        <label class="el-form-item-label">创建类型</label>
        <el-select v-model="query.createType" filterable clearable placeholder="请选择" class="filter-item">
          <el-option
            v-for="item in dict.create_type"
            :key="item.id"
            :label="item.label"
            :value="item.value" />
        </el-select>
        <label class="el-form-item-label">报告类型</label>
        <el-select v-model="query.reportType" filterable clearable placeholder="请选择" class="filter-item">
          <el-option
            v-for="item in dict.amazon_report_type"
            :key="item.id"
            :label="item.label"
            :value="item.value" />
        </el-select>
        <br />
        <label class="el-form-item-label">报告处理状态</label>
        <el-select v-model="query.processingStatuses" filterable clearable placeholder="请选择" class="filter-item">
          <el-option
            v-for="item in dict.processing_statuses"
            :key="item.id"
            :label="item.label"
            :value="item.value" />
        </el-select>
<!--        <el-input v-model="query.processingStatuses" clearable placeholder="报告处理状态" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />-->
        <label class="el-form-item-label">报告数据开始时间</label>
        <date-range-picker
          v-model="query.dataStartTime"
          start-placeholder="开始时间"
          end-placeholder="结束时间"
          class="date-item"
        />
        <br />
        <label class="el-form-item-label">报告数据截止时间</label>
        <date-range-picker
          v-model="query.dataEndTime"
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
          <el-form-item label="站点ID">
            <el-select v-model="form.marketplaceId" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.market_place_id"
                :key="item.id"
                :label="item.label"
                :value="item.value" />
            </el-select>
          </el-form-item>
          <el-form-item label="报告状态">
            <el-select v-model="form.reportStatus" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.report_status"
                :key="item.id"
                :label="item.label"
                :value="item.value" />
            </el-select>
          </el-form-item>
          <el-form-item label="创建类型">
            <el-select v-model="form.createType" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.create_type"
                :key="item.id"
                :label="item.label"
                :value="item.value" />
            </el-select>
          </el-form-item>
          <el-form-item label="报告说明">
            <el-input v-model="form.reportMsg" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="报告ID">
            <el-input v-model="form.reportId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="报告类型">
            <el-select v-model="form.reportType" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.amazon_report_type"
                :key="item.id"
                :label="item.label"
                :value="item.value" />
            </el-select>
          </el-form-item>
          <el-form-item label="数据开始时间">
            <el-date-picker v-model="form.dataStartTime" type="datetime" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="数据截止时间">
            <el-date-picker v-model="form.dataEndTime" type="datetime" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="报告创建时间">
            <el-input v-model="form.createdTime" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="报告处理状态">
            <el-select v-model="form.processingStatuses" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.processing_statuses"
                :key="item.id"
                :label="item.label"
                :value="item.value" />
            </el-select>
          </el-form-item>
          <el-form-item label="报告生产开始时间">
            <el-input v-model="form.processingStartTime" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="报告生产截止时间">
            <el-input v-model="form.processingEndTime" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="报告文档ID">
            <el-input v-model="form.reportDocumentId" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="报告文档下载地址">
            <el-input v-model="form.reportDocumentUrl" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="报告文档下载地址">
            <el-input v-model="form.reportDocumentName" style="width: 370px;" />
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
        <el-table-column prop="sellingPartnerId" label="店铺ID" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="reportId" label="报告ID" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="marketplaceId" label="站点ID" width="150" align="center" show-overflow-tooltip="true">
          <template slot-scope="scope">
            {{ dict.label.market_place_id[scope.row.marketplaceId] }}
          </template>
        </el-table-column>
        <el-table-column prop="reportStatus" label="报告状态" width="150" align="center" show-overflow-tooltip="true">
          <template slot-scope="scope">
            {{ dict.label.report_status[scope.row.reportStatus] }}
          </template>
        </el-table-column>
        <el-table-column prop="createType" label="创建类型" width="150" align="center" show-overflow-tooltip="true">
          <template slot-scope="scope">
            {{ dict.label.create_type[scope.row.createType] }}
          </template>
        </el-table-column>
        <el-table-column prop="reportMsg" label="报告说明" width="150" align="center" show-overflow-tooltip="true" />

        <el-table-column prop="reportType" label="报告类型" width="150" align="center" show-overflow-tooltip="true">
          <template slot-scope="scope">
            {{ dict.label.amazon_report_type[scope.row.reportType] }}
          </template>
        </el-table-column>
        <el-table-column prop="dataStartTime" label="数据开始时间" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="dataEndTime" label="数据截止时间" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="createdTime" label="报告创建时间" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="processingStatuses" label="报告处理状态" width="150" align="center" show-overflow-tooltip="true">
          <template slot-scope="scope">
            {{ dict.label.processing_statuses[scope.row.processingStatuses] }}
          </template>
        </el-table-column>
        <el-table-column prop="processingStartTime" label="报告生产开始时间" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="processingEndTime" label="报告生产截止时间" width="150" align="center" show-overflow-tooltip="true" />
<!--        <el-table-column prop="reportDocumentId" label="报告文档ID" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="reportDocumentUrl" label="报告文档下载地址" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="reportDocumentName" label="报告文档下载地址" width="150" align="center" show-overflow-tooltip="true" />-->
        <el-table-column prop="rowCrtTs" label="创建日期" width="150" align="center" show-overflow-tooltip="true" />
        <el-table-column prop="rowUptTs" label="更新时间" width="150" align="center" show-overflow-tooltip="true" />
<!--        <el-table-column v-if="checkPer(['admin','dcAmazonReport:edit','dcAmazonReport:del'])" label="操作" width="150px" align="center">
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
import crudDcAmazonReport from '@/api/dcAmazonReport'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'
import DateRangePicker from '@/components/DateRangePicker'

const defaultForm = { rowId: null, sellingPartnerId: null, marketplaceId: null, reportStatus: null, createType: null, reportMsg: null, reportId: null, reportType: null, dataStartTime: null, dataEndTime: null, createdTime: null, processingStatuses: null, processingStartTime: null, processingEndTime: null, reportDocumentId: null, reportDocumentUrl: null, reportDocumentName: null, rowCrtTs: null, rowUptTs: null, rowTp: null }
export default {
  name: 'DcAmazonReport',
  components: { pagination, crudOperation, rrOperation, udOperation, DateRangePicker },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  dicts: ['market_place_id', 'report_status', 'create_type', 'amazon_report_type', 'processing_statuses'],
  cruds() {
    return CRUD({ title: '亚马逊报告记录管理', url: 'api/dcAmazonReport', idField: 'rowId', sort: 'rowId,desc', crudMethod: { ...crudDcAmazonReport },
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
        add: ['admin', 'dcAmazonReport:add'],
        edit: ['admin', 'dcAmazonReport:edit'],
        del: ['admin', 'dcAmazonReport:del']
      },
      rules: {
      },
      queryTypeOptions: [
        { key: 'sellingPartnerId', display_name: '店铺ID' },
        { key: 'marketplaceId', display_name: '站点ID' },
        { key: 'reportStatus', display_name: '报告状态' },
        { key: 'createType', display_name: '创建类型' },
        { key: 'reportId', display_name: '报告ID' },
        { key: 'reportType', display_name: '报告类型' },
        { key: 'processingStatuses', display_name: '报告处理状态' }
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
