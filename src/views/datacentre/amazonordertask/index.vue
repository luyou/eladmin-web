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
        <label class="el-form-item-label">初始化采集状态</label>
        <el-select v-model="query.initCollectionStatus" filterable clearable placeholder="请选择" class="filter-item">
          <el-option
            v-for="item in dict.init_collection_status"
            :key="item.id"
            :label="item.label"
            :value="item.value"
          />
        </el-select>
        <br/>
        <label class="el-form-item-label">采集状态</label>
        <el-select v-model="query.collectionStatus" filterable clearable placeholder="请选择" class="filter-item">
          <el-option
            v-for="item in dict.collection_status"
            :key="item.id"
            :label="item.label"
            :value="item.value"
          />
        </el-select>
        <label class="el-form-item-label">最后采集日期</label>
        <date-range-picker
          v-model="query.lastTime"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
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
          <el-form-item label="采集管理状态">
            <el-select v-model="form.mgrStatus" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.mgr_status"
                :key="item.id"
                :label="item.label"
                :value="item.value" />
            </el-select>
          </el-form-item>
          <el-form-item label="初始化采集状态">
            <el-select v-model="form.initCollectionStatus" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.init_collection_status"
                :key="item.id"
                :label="item.label"
                :value="item.value" />
            </el-select>
          </el-form-item>
          <el-form-item label="初始化采集截止日期">
            <el-date-picker v-model="form.initEndDate" type="datetime" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="采集开始时间">
            <el-date-picker v-model="form.startTime" type="datetime" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="最后采集时间">
            <el-date-picker v-model="form.lastTime" type="datetime" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="采集状态">
            <el-select v-model="form.collectionStatus" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.collection_status"
                :key="item.id"
                :label="item.label"
                :value="item.value" />
            </el-select>
          </el-form-item>
          <el-form-item label="订单采集描述">
            <el-input v-model="form.collectionMsg" :rows="3" type="textarea" style="width: 370px;" />
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
        <el-table-column prop="sellingPartnerId" label="店铺ID" align="center" width="150" />
        <el-table-column prop="countryCode" label="国家代码" align="center">
          <template slot-scope="scope">
            {{ dict.label.country_code[scope.row.countryCode] }}
          </template>
        </el-table-column>
        <el-table-column prop="collectionStatus" label="采集状态" align="center">
          <template slot-scope="scope">
            {{ dict.label.collection_status[scope.row.collectionStatus] }}
          </template>
        </el-table-column>
        <el-table-column prop="initCollectionStatus" label="初始化采集状态" align="center">
          <template slot-scope="scope">
            {{ dict.label.init_collection_status[scope.row.initCollectionStatus] }}
          </template>
        </el-table-column>
        <el-table-column prop="mgrStatus" label="采集管理状态" align="center">
          <template slot-scope="scope">
            {{ dict.label.mgr_status[scope.row.mgrStatus] }}
          </template>
        </el-table-column>
        <el-table-column prop="initEndDate" label="初始化采集截止日期" align="center" width="150" />
        <el-table-column prop="startTime" label="采集开始时间" align="center" width="150" />
        <el-table-column prop="lastTime" label="最后采集时间" align="center" width="150" />

        <el-table-column prop="collectionMsg" label="订单采集描述" align="center" />
        <el-table-column prop="rowCrtTs" label="创建时间" align="center" width="150" />
        <el-table-column prop="rowUptTs" label="更新时间" align="center" width="150" />
        <el-table-column v-if="checkPer(['admin','dcAmazonOrderTask:edit','dcAmazonOrderTask:del'])" label="操作" width="150px" fixed="right" align="center">
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
import crudDcAmazonOrderTask from '@/api/dcAmazonOrderTask'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'
import DateRangePicker from '@/components/DateRangePicker'

const defaultForm = { rowId: null, sellingPartnerId: null, countryCode: null, mgrStatus: null, initCollectionStatus: null, initEndDate: null, startTime: null, lastTime: null, collectionStatus: null, collectionMsg: null, rowCrtTs: null, rowUptTs: null, rowTp: null }
export default {
  name: 'DcAmazonOrderTask',
  components: { pagination, crudOperation, rrOperation, udOperation, DateRangePicker },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  dicts: ['country_code', 'mgr_status', 'init_collection_status', 'collection_status'],
  cruds() {
    return CRUD(
      { title: '亚马逊订单采集任务', url: 'api/dcAmazonOrderTask', idField: 'rowId', sort: 'rowId,desc', crudMethod: { ...crudDcAmazonOrderTask },
      optShow: {
        add: false,
        edit: true,
        del: true,
        download: true,
        reset: true
      }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'dcAmazonOrderTask:add'],
        edit: ['admin', 'dcAmazonOrderTask:edit'],
        del: ['admin', 'dcAmazonOrderTask:del']
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
        { key: 'initCollectionStatus', display_name: '初始化采集状态' },
        { key: 'collectionStatus', display_name: '采集状态' }
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
