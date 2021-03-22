<template>
  <a-card
    style="width:100%;padding:10px"
  >
    <a-spin :spinning="spinning">
      <a-tabs type="card" default-active-key="1" @change="handleTabs">
        <div slot="tabBarExtraContent">
          <a-button>快速导入</a-button>
          <a-button :style="{ marginLeft: '10px' }">物模型 TSL</a-button>
        </div>
        <a-tab-pane key="1" tab="属性定义">
          <card-table
            :title="'属性定义'"
            :tabKey="tabKey"
            :attributeColumns="attrColumns"
            :showData="propertyData"
            :edititem="editPropertiesData"
            :showDrawer="showDrawer"
            @close="onCloseDrawer"
            @onEditItem="onEditItem"
            @addData="onOpenDrawer"
          />
        </a-tab-pane>
        <a-tab-pane key="2" tab="功能定义">
          <card-table
            :title="'功能定义'"
            :tabKey="tabKey"
            :attributeColumns="funcColumns"
            :showData="funcData"
            :edititem="editPropertiesData"
            :showDrawer="showDrawer"
            @close="onCloseDrawer"
            @onEditItem="onEditItem"
            @addData="onOpenDrawer"
          />
        </a-tab-pane>
        <a-tab-pane key="3" tab="事件定义">
          <!-- <card-table :title="'事件定义'" :tabKey="tabKey" :attributeColumns="columns" :showData="data"/> -->
        </a-tab-pane>
        <a-tab-pane key="4" tab="标签定义">
          <!-- <card-table :title="'标签定义'" :tabKey="tabKey" :attributeColumns="columns" :showData="data"/> -->
        </a-tab-pane>
      </a-tabs>
    </a-spin>
  </a-card>
</template>

<script>
  import { mapGetters } from 'vuex'
  import CardTable from '../components/CardTable'
  const data = [
    {
      key: '1',
      id: '1',
      name: 'John Brown',
      age: 32,
      valueType: { type: 'string' },
      'expands.readOnly': true,
      description: '131244'
    }
  ]
  export default {
    name: 'ProPhyModel',
    components: {
      CardTable
    },
    data () {
      const attrColumns = [
        {
          title: '属性标识',
          key: 'id',
          dataIndex: 'id'
        },
        {
          title: '属性名称',
          key: 'name',
          dataIndex: 'name'
        },
        {
          title: '数据类型',
          key: 'valueType',
          dataIndex: 'valueType',
          customRender: text => text.type
        },
        {
          title: '是否只读',
          key: 'expands.readOnly',
          dataIndex: 'expands.readOnly',
          customRender: text => ((text === 'true' || text === true) ? '是' : '否')
        },
        {
          title: '说明',
          key: 'description',
          dataIndex: 'description',
          width: '30%',
          ellipsis: true
        },
        {
          title: '操作',
          // width: '180px',
          key: 'operation',
          customRender: (text, record) => (
            <div>
              <a onClick={() => this.editItem(record)}>编辑</a>
              <a-divider type="vertical"/>
              <a onClick={() => ''}>删除</a>
            </div>
          )
          // scopedSlots: { customRender: 'operation' }
        }
      ]
      const funcColumns = [
        {
          title: '功能标识',
          dataIndex: 'id'
        },
        {
          title: '名称',
          dataIndex: 'name'
        },
        {
          title: '是否异步',
          dataIndex: 'async',
          customRender: text => (text ? '是' : '否')
        },
        {
          title: '描述',
          dataIndex: 'description',
          width: '30%',
          ellipsis: true
        },
        {
          title: '操作',
          width: '250px',
          align: 'center',
          customRender: (text, record) => (
            <div>
              <a onClick={() => this.editItem(record)}>编辑</a>
              <a-divider type="vertical" />
              <a onClick={() => ''}>删除</a>
            </div>
          )
        }
      ]
      return {
        spinning: true,
        editPropertiesData: {},
        editFuncData: {},
        tabKey: '1',
        propertyData: [],
        funcData: [],
        showDrawer: false,
        data,
        attrColumns: attrColumns,
        funcColumns: funcColumns
      }
    },
    mounted () {
      this.InitData()
    },
    watch: {
      tabKey: {
        handler (newKey, oldKey) {
          this.InitData(newKey)
        }
      }
    },
    computed: {
      ...mapGetters('device', ['productDetailData']),
      getDeviceId () {
        return this.$route.params.id
      }
    },
    methods: {
      InitData () {
        const { metadata } = this.productDetailData(this.getDeviceId)[0]
        switch (this.tabKey) {
          case '1':
            this.propertyData = JSON.parse(metadata).properties
            console.log(this.propertyData, 111111111111)
            this.spinning = false
            break
          case '2':
            this.funcData = JSON.parse(metadata).functions
            console.log(this.funcData, 111111111111)
            this.spinning = false
            break
          default:
            break
        }
      },
      handleTabs (key) {
        this.tabKey = key
      },
      editItem (data) {
        this.editPropertiesData = data
        this.showDrawer = true
      },
      onCloseDrawer () {
        this.editPropertiesData = {}
        this.editFuncData = {}
        this.showDrawer = false
      },
      onEditItem (metadata) {
        // this.InitData()
        const listData = this.productDetailData(this.getDeviceId)[0]
        this.propertyData = metadata.properties
        this.$store.commit('device/EDIT_PRODUCT_LIST', {
          ...listData,
          metadata: JSON.stringify(metadata)
        })
        this.showDrawer = false
      },
      onOpenDrawer () {
        this.showDrawer = true
      }
    }
  }
</script>

<style lang="scss" scoped>

</style>
