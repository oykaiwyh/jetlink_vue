<template>
  <a-drawer
    :visible="visible"
    :width="width"
    :title="`${ data.id ? '编辑' : '新建'}证书`"
    @close="onClose"
  >
    <a-form
      :labelCol="{ span: 6 }"
      :wrapperCol="{ span: 18 }"
    >
      <a-row>
        <a-col :span="12">
          <a-form-item key="id" label="协议ID" >
            <a-input
              v-decorator="['id', {
                rules: [
                  { required: true, message: '协议标识' }
                ],
              }]"
              :default-value="data.id ? data.id: ''"
              placeholder="请输入协议ID"
              :disabled="data.id ? true : false"
            />
          </a-form-item>
        </a-col>
        <a-col :span="12">
          <a-form-item key="name" label="协议名称" >
            <a-input
              :value="data.name ? data.name: ''"
              v-decorator="['name', {
                rules: [
                  { required: true, message: '协议名称' }
                ],
              }]"
              placeholder="请输入协议名称"
            />
          </a-form-item>
        </a-col>
        <a-col :span="12">
          <a-form-item key="type" label="类型" >
            <a-select
              v-decorator="['type', {
                rules: [
                  { required: true }
                ],
              }]"
              :default-value="data.type ? data.type: undefined"
              @change="SelectProtocolType"
              placeholder="请选择协议类型"
            >
              <a-select-option v-for="(item, index) in protocolTypeOptions" :key="item.id+index" :value="item.id">
                {{ item.text }}
              </a-select-option>
            </a-select>
          </a-form-item>
        </a-col>
      </a-row>
      <a-row v-if="selectProtocolType === 'script' ">
        <a-col :span="12">
          <a-form-item key="protocol" label="协议标识" >
            <a-input
              v-decorator="['configuration.protocol', {
                rules: [
                  { required: true, message: '请输入协议标识' }
                ],
              }]"
              :default-value="data.configuration?data.configuration.protocol?data.configuration.protocol:undefined:'' "
              placeholder="请输入协议ID"
            />
          </a-form-item>
        </a-col>
        <a-col :span="12">
          <a-form-item key="transport" label="连接协议" >
            <a-select
              v-decorator="['configuration.transport', {
                rules: [
                  { required: true , message: '请输入连接协议'}
                ],
              }]"
              mode="multiple"
              :default-value="data.configuration? data.configuration.transport.split(','):undefined"
              @change="SelectTransport"
              placeholder="请选择协议类型"
            >
              <a-select-option v-for="(item) in transportOptions" :key="item" :value="item">
                {{ item }}
              </a-select-option>
            </a-select>
          </a-form-item>
        </a-col>
      </a-row>
      <a-form-item key="description" label="描述" :labelCol="{ span: 3 }" :wrapperCol="{ span: 21 }">
        <a-input
          :value="data.description ? data.description: ''"
          v-decorator="['description', {
            rules: [
              { required: true }
            ],
          }]"
          placeholder="请输入"
        />
      </a-form-item>
    </a-form>
    <div
      :style="{
        position: 'absolute',
        right: 0,
        bottom: 0,
        width: '100%',
        borderTop: '1px solid #e9e9e9',
        padding: '10px 16px',
        background: '#fff',
        textAlign: 'right',
        zIndex: 1,
      }"
    >
      <a-button :style="{ marginRight: '8px' }" @click="onClose">
        关闭
      </a-button>
      <a-button type="primary" >
        确认
      </a-button>
    </div>
  </a-drawer>
</template>

<script>
  import apis from '@/api'
  import { HandleProviders } from '../service'
  export default {
    name: 'ProtoSaveModal',
    props: {
      visible: {
        type: Boolean,
        default: false
      },
      width: {
        type: Number,
        default: 900
      },
      data: {
        type: Object,
        default: () => {}
      }
    },
    computed: {
      getDefaultValue () {
        return function (key, value = '') {
          const { data } = this
          const keys = data.split('.')
          if (keys.length > 1) {
            if (data[keys[0]]) {
            }
          }
          if (data[key]) {
            return data[key]
          }
          return value
        }
      }
    },
    watch: {
      visible: {
        handler (newVal, oldVal) {
          if (newVal === true) {
            console.log('this.data', this.data)
            this.Init()
            this.GetProviders()
          }
        }
      }
    },
    data () {
      return {
        protocolTypeOptions: [],
        selectProtocolType: '',
        transportOptions: [ 'MQTT', 'UDP', 'CoAP', 'TCP', 'HTTP', 'HTTPS' ],
        selectTransportType: ''
      }
    },
    methods: {
      Init () {
        this.selectProtocolType = this.data.type
      },
      async GetProviders () {
        const result = await apis.network.GetProviders()
        const handleData = HandleProviders(result)
        this.protocolTypeOptions = handleData.data
      },
      SelectProtocolType (value) {
        this.selectProtocolType = value
      },
      SelectTransport (value) {
        this.selectTransportType = value
      },
      onClose (e) {
        this.$emit('onClose')
      },
      getAccessToken () {
      }
    }
  }
</script>

<style lang="scss" scoped>

</style>
