<template>
  <div class="app-container">
    <!--工具栏-->
    <div class="head-container">
      <div v-if="crud.props.searchToggle">
        <!-- 搜索 -->
        <label class="el-form-item-label">店铺ID</label>
        <el-input v-model="query.sellingPartnerId" clearable placeholder="店铺ID" style="width: 185px;" class="filter-item" @keyup.enter.native="crud.toQuery" />
        <label class="el-form-item-label">令牌类型</label>
        <el-select v-model="query.tokenType" filterable clearable placeholder="请选择" class="filter-item">
          <el-option
            v-for="item in dict.token_type"
            :key="item.id"
            :label="item.label"
            :value="item.value"
          />
        </el-select>
        <br/>
        <label class="el-form-item-label">授权时间</label>
        <date-range-picker
          v-model="query.spapiOauthTime"
          start-placeholder="开始时间"
          end-placeholder="结束时间"
          class="date-item"
        />
        <label class="el-form-item-label">更新时间</label>
        <date-range-picker
          v-model="query.rowUptTs"
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
          <el-form-item label="授权时间" prop="spapiOauthTime">
            <el-date-picker v-model="form.spapiOauthTime" type="datetime" value-format="yyyy-MM-dd HH:mm:ss" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="临时令牌">
            <el-input v-model="form.accessToken" :rows="3" type="textarea" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="刷新令牌" prop="refreshToken">
            <el-input v-model="form.refreshToken" :rows="3" type="textarea" style="width: 370px;" />
          </el-form-item>
          <el-form-item label="令牌类型">
            <el-select v-model="form.tokenType" filterable placeholder="请选择">
              <el-option
                v-for="item in dict.token_type"
                :key="item.id"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
          </el-form-item>
          <el-form-item label="过期日期">
            <el-date-picker v-model="form.expiresDate" type="datetime" value-format="yyyy-MM-dd HH:mm:ss" style="width: 370px;" />
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
        <el-table-column prop="spapiOauthCode" label="授权oauth_code" align="center" width="180" />
        <el-table-column prop="spapiOauthState" label="授权oauth_state" align="center" width="120" />
        <el-table-column prop="spapiOauthTime" label="授权时间" align="center" width="150" />
        <el-table-column prop="tokenType" label="令牌类型" align="center" width="100">
          <template slot-scope="scope">
            {{ dict.label.token_type[scope.row.tokenType] }}
          </template>
        </el-table-column>
        <el-table-column prop="expiresDate" label="过期日期" align="center" width="150" />
        <el-table-column prop="rowCrtTs" label="创建日期" align="center" width="150" />
        <el-table-column prop="rowUptTs" label="更新时间" align="center" width="150" />
        <el-table-column v-if="checkPer(['admin','dcAmazonSeller:edit','dcAmazonSeller:del'])" label="操作" width="150px" align="center" fixed="right">
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
import crudDcAmazonSeller from '@/api/dcAmazonSeller'
import CRUD, { presenter, header, form, crud } from '@crud/crud'
import rrOperation from '@crud/RR.operation'
import crudOperation from '@crud/CRUD.operation'
import udOperation from '@crud/UD.operation'
import pagination from '@crud/Pagination'
import DateRangePicker from '@/components/DateRangePicker'

const defaultForm = { rowId: null, sellingPartnerId: null, spapiOauthCode: null, spapiOauthState: null, spapiOauthTime: null, accessToken: null, refreshToken: null, tokenType: null, expiresDate: null, rowCrtTs: null, rowUptTs: null, rowTp: null }
export default {
  name: 'DcAmazonSeller',
  components: { pagination, crudOperation, rrOperation, udOperation, DateRangePicker },
  mixins: [presenter(), header(), form(defaultForm), crud()],
  dicts: ['token_type'],
  cruds() {
    return CRUD({ title: '亚马逊店铺授权信息', url: 'api/dcAmazonSeller', idField: 'rowId', sort: 'rowUptTs,desc', crudMethod: { ...crudDcAmazonSeller }})
  },
  data() {
    return {
      permission: {
        add: ['admin', 'dcAmazonSeller:add'],
        edit: ['admin', 'dcAmazonSeller:edit'],
        del: ['admin', 'dcAmazonSeller:del']
      },
      rules: {
        sellingPartnerId: [
          { required: true, message: '店铺ID不能为空', trigger: 'blur' }
        ],
        spapiOauthTime: [
          { required: true, message: '授权时间不能为空', trigger: 'blur' }
        ],
        refreshToken: [
          { required: true, message: '刷新令牌不能为空', trigger: 'blur' }
        ]
      },
      queryTypeOptions: [
        { key: 'sellingPartnerId', display_name: '店铺ID' },
        { key: 'tokenType', display_name: '令牌类型' }
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
