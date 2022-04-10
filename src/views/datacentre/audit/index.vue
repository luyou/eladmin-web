<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
        <label class="el-form-item-label">店铺ID</label>
        <el-input v-model="query.sellingPartnerId" clearable placeholder="店铺ID" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">类型</label>
        <el-select v-model="query.eventType" clearable placeholder="请选择" class="filter-item">
          <el-option
            v-for="item in dict.event_type"
            :key="item.id"
            :label="item.label"
            :value="item.value" />
        </el-select>
        <label class="el-form-item-label">处理状态</label>
        <el-select v-model="query.eventStatus" clearable placeholder="请选择" class="filter-item">
          <el-option
            v-for="item in dict.event_status"
            :key="item.id"
            :label="item.label"
            :value="item.value" />
        </el-select>
        <br/>
        <label class="el-form-item-label">记录时间</label>
        <date-range-picker
          v-model="query.rowCrtTs"
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
          <el-form-item label="类型" prop="eventType">
            <el-select v-model="form.eventType" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.event_type"
                :key="item.id"
                :label="item.label"
                :value="item.value" />
            </el-select>
          </el-form-item>
          <el-form-item label="内容" prop="eventContent">
            <el-input v-model="form.eventContent" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="补充说明">
            <el-input v-model="form.eventContentAdditional" :rows="3" type="textarea" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="处理状态" prop="eventStatus">
            <el-select v-model="form.eventStatus" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.event_status"
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
      <el-table ref="table" v-loading="crud.loading" :data="crud.data"  style="width: 100%;" highlight-current-row="true" @selection-change="crud.selectionChangeHandler">
        <el-table-column type="selection" width="55" />
<!--        <el-table-column prop="rowId" label="ID" />-->
        <el-table-column prop="sellingPartnerId" label="店铺ID" min-width="100" />
        <el-table-column prop="eventType" label="类型">
          <template slot-scope="scope">
            {{ dict.label.event_type[scope.row.eventType] }}
          </template>
        </el-table-column>
        <el-table-column prop="eventContent" label="内容" />
        <el-table-column prop="eventContentAdditional" label="补充说明" />
        <el-table-column prop="eventStatus" label="处理状态">
          <template slot-scope="scope">
            {{ dict.label.event_status[scope.row.eventStatus] }}
          </template>
        </el-table-column>
        <el-table-column prop="rowCrtTs" label="创建日期" />
        <el-table-column prop="rowUptTs" label="更新时间" />
        <el-table-column v-if="checkPer(['admin','dcEventAudit:edit','dcEventAudit:del'])" label="操作" width="150px" align="center" fixed="right">
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
import crudDcEventAudit from '@/api/dcEventAudit'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'
import DateRangePicker from '@/components/DateRangePicker'

const defaultForm = { rowId: null, sellingPartnerId: null, eventType: null, eventContent: null, eventContentAdditional: null, eventStatus: null, rowCrtTs: null, rowUptTs: null, rowTp: null }
export default {
  name: 'DcEventAudit',
  components: { pagination, crudOperation, rrOperation, udOperation, DateRangePicker },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  dicts: ['event_type', 'event_status'],
  cruds() {
    return CRUD({ title: '数据中心异常事件审计', url: 'api/dcEventAudit', idField: 'rowId', sort: 'rowUptTs,desc', crudMethod: { ...crudDcEventAudit }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'dcEventAudit:add'],
        edit: ['admin', 'dcEventAudit:edit'],
        del: ['admin', 'dcEventAudit:del']
      },
      rules: {
        sellingPartnerId: [
          { required: true, message: '店铺ID不能为空', trigger: 'blur' }
        ],
        eventType: [
          { required: true, message: '类型不能为空', trigger: 'blur' }
        ],
        eventContent: [
          { required: true, message: '内容不能为空', trigger: 'blur' }
        ],
        eventStatus: [
          { required: true, message: '处理状态不能为空', trigger: 'blur' }
        ]
      },
      queryTypeOptions: [
        { key: 'sellingPartnerId', display_name: '店铺ID' },
        { key: 'eventType', display_name: '类型' },
        { key: 'eventStatus', display_name: '处理状态' }
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
