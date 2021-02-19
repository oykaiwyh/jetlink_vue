<template>
  <page-header-wrapper :title="GetMetaTitle">
    <template v-slot:extra v-if="GetDetailStatus">
      <div class="page-header-extra">
        <div class="content">
          <div class="content-status">
            <a-badge status="success" />已发布
            <a-button type="link">
              停用
            </a-button>
          </div>
          <div class="content-restart">
            <a-button type="primary"><a-icon type="sync" />应用配置</a-button>
          </div>
        </div>
      </div>
    </template>
    <template v-slot:content v-if="GetDetailStatus">
      <div class="page-header-product-num">
        <div class="content">
          <div class="content-title">
            设备数量 1
            <a-button type="link">
              查看
            </a-button>
          </div>
        </div>
      </div>
    </template>
    <div v-if="GetRouteShow">
      <a-card :bordered="false">
        <div class="table-page-search-wrapper">
          <a-form layout="inline">
            <a-row :gutter="48">
              <a-col :md="8" :sm="24">
                <a-form-item label="产品名称">
                  <a-input v-model="queryParam.id" placeholder=""/>
                </a-form-item>
              </a-col>
              <a-col :md="8" :sm="24">
                <a-form-item label="所属品类">
                  <a-select v-model="queryParam.status" placeholder="请选择" default-value="0">
                    <a-select-option value="0">全部</a-select-option>
                    <a-select-option value="1">关闭</a-select-option>
                    <a-select-option value="2">运行中</a-select-option>
                  </a-select>
                </a-form-item>
              </a-col>
              <template v-if="advanced">
                <a-col :md="8" :sm="24">
                  <a-form-item label="产品类型">
                    <a-select
                      mode="multiple"
                      placeholder="产品类型"
                      style="width: 100%"
                      v-model="queryParam.callNo"
                    >
                      <a-select-option v-for="i in 2" :key="(i + 9).toString(36) + i">
                        {{ (i + 9).toString(36) + i }}
                      </a-select-option>
                    </a-select>
                  </a-form-item>
                </a-col>
                <a-col :md="8" :sm="24">
                  <a-form-item label="消息协议">
                    <a-select
                      mode="multiple"
                      placeholder="消息协议"
                      style="width: 100%"
                      v-model="queryParam.date"
                    >
                      <a-select-option v-for="i in 2" :key="(i + 9).toString(36) + i">
                        {{ (i + 9).toString(36) + i }}
                      </a-select-option>
                    </a-select>
                  </a-form-item>
                </a-col>
              </template>
              <a-col :md="!advanced && 8 || 24" :sm="24">
                <span class="table-page-search-submitButtons" :style="advanced && { float: 'right', overflow: 'hidden' } || {} ">
                  <a-button type="primary" @click="() => this.queryParam = {}">查询</a-button>
                  <a-button style="margin-left: 8px" @click="() => this.queryParam = {}">重置</a-button>
                  <a @click="toggleAdvanced" style="margin-left: 8px">
                    {{ advanced ? '收起' : '展开' }}
                    <a-icon :type="advanced ? 'up' : 'down'"/>
                  </a>
                </span>
              </a-col>
            </a-row>
          </a-form>
        </div>

        <div class="table-operator">
          <a-button type="primary" icon="plus" @click="handleAdd">新建</a-button>
          <a-upload
            name="file"
            :multiple="true"
            action="https://www.mocky.io/v2/5cc8019d300000980a055e76"
          >
            <a-button> <a-icon type="upload" /> 快速导入 </a-button>
          </a-upload>
        </div>
      </a-card>
      <div class="ant-pro-pages-list-applications-filterCardList">
        <a-list :loading="loading" :data-source="data" :grid="{ gutter: 24, xl: 4, lg: 3, md: 3, sm: 2, xs: 1 }" style="margin-top: 24px;">
          <a-list-item slot="renderItem" slot-scope="item">
            <a-card :body-style="{ paddingBottom: 20 }" hoverable>
              <a-card-meta :title="item.title">
                <template slot="avatar">
                  <a-avatar size="small" :src="item.avatar" style="width: 40px; height: 40px; line-height: 40px; font-size: 18px;"/>
                </template>
                <template slot="description">
                  www.instagram.com
                </template>
              </a-card-meta>
              <template slot="actions">
                <a-tooltip title="查看">
                  <a-icon type="eye" @click="() => { $router.push({ path: '/device/product/save/2' }) }"/>
                </a-tooltip>
                <a-tooltip title="编辑">
                  <a-icon type="edit" />
                </a-tooltip>
                <a-tooltip title="下载">
                  <a-icon type="download" />
                </a-tooltip>
                <a-dropdown>
                  <a class="ant-dropdown-link">
                    <a-icon type="ellipsis" />
                  </a>
                  <a-menu slot="overlay">
                    <a-menu-item>
                      <a-button type="link">
                        <a-icon type="close" />
                        <a href="javascript:;">停用</a>
                      </a-button>
                    </a-menu-item>
                    <a-menu-item>
                      <a-button type="link">
                        <a-icon type="stop" />
                        <a href="javascript:;">删除</a>
                      </a-button>
                    </a-menu-item>
                  </a-menu>
                </a-dropdown>
              </template>
              <div class="">
                <card-info active-user="100" new-user="999"></card-info>
              </div>
            </a-card>
          </a-list-item>
        </a-list>
      </div>
    </div>
    <router-view></router-view>
  </page-header-wrapper>
</template>

<script>
import { getServiceList } from '@/api/manage'
import CardInfo from './components/CardInfo'

export default {
  name: 'DeviceProduct',
  components: {
    CardInfo
  },
  data () {
    return {
      // 高级搜索 展开/关闭
      advanced: false,
      // 查询参数
      queryParam: {},
      // 加载数据方法 必须为 Promise 对象
      loadData: parameter => {
        const requestParameters = Object.assign({}, parameter, this.queryParam)
        console.log('loadData request parameters:', requestParameters)
        return getServiceList(requestParameters)
          .then(res => {
            return res.result
          })
      },
      selectedRowKeys: [],
      selectedRows: [],
      data: [],
      loading: true

    }
  },
  computed: {
    rowSelection () {
      return {
        selectedRowKeys: this.selectedRowKeys,
        onChange: this.onSelectChange
      }
    },
    GetMetaTitle () {
      if (this.$route.path.includes('/device/product/save')) {
        return this.$route.params.id
      }
      return this.$route.meta.title || ''
    },
    GetDetailStatus () {
      console.log(this.$route.path.includes('/device/product/save'))
      return this.$route.path.includes('/device/product/save')
    },
    GetRouteShow () {
      return this.$route.path === '/device/product'
    }
  },
  mounted () {
    this.getList()
  },
  methods: {
    getList () {
      this.$http.get('/list/article', { params: { count: 8 } }).then(res => {
        console.log('res', res)
        this.data = res.result
        this.loading = false
      })
    },
    handleAdd () {
      this.$router.push({
        path: '/device/product/add'
      })
    },
    onSelectChange (selectedRowKeys, selectedRows) {
      this.selectedRowKeys = selectedRowKeys
      this.selectedRows = selectedRows
    },
    toggleAdvanced () {
      this.advanced = !this.advanced
    }
  }
}
</script>
<style lang="less" scoped>
  /deep/ .ant-page-header-heading {
    display: flex;
    justify-content: flex-start;
  }
  /deep/ .ant-page-header-heading-title {
    flex-grow: 5;
  }
  /deep/ .ant-page-header-heading-extra {
    flex-grow: 5;
  }
  .page-header-extra{
    .content{
      display: flex !important;
      justify-content: space-between;
    }
  }
</style>
