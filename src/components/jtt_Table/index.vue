<template>
  <a-table :columns="attributeColumns" :data-source="showData">
    <template slot="operation">
      <template v-for="(item, index) in actions">
        <a-button :key="'tableColoumns'+index" type="link" style="padding: 0;">{{ item.name }}</a-button>
        <a-divider :key="'divider'+index" type="vertical" v-if="index!==actions.length-1"/>
      </template>
    </template>
  </a-table>
</template>

<script>
  export default {
    name: 'ComTable',
    props: {
      attributeColumns: {
        type: Array,
        default: () => []
      },
      showData: {
        type: Array,
        default: () => []
      },
      actions: {
        type: Array,
        default: () => []
      }
    }
  }
</script>

<style lang="scss" scoped>

</style>
