<template>
  <tr 
    class="list-item" 
    @click="handleSelectRow($event, $refs[rowData.name], rowData)">
    <td class="list-item-checkbox">
      <input type="checkbox" :name="rowData.name" :ref="rowData.name" />
    </td>
    <td class="list-item-name">{{ rowData.name }}</td>
    <td class="list-item-device">{{ rowData.device }}</td>
    <td class="list-item-path">{{ rowData.path }}</td>
    <td class="list-item-status" :class="rowData.status">{{ rowData.status }}</td>
  </tr>
</template>

<script>
export default {
  name: "SelectableListItem",
  props: {
    rowData: {
      name: String,
      device: String,
      path: String,
      status: String,
    },
    handleSelectRow: {
      type: Function
    }
  }
};
</script>

<style scoped lang="scss">
.list-item {
  cursor: pointer;

  &:hover {
    background-color: #f2efef;
  }

  &.active {
    background-color: #e3e1e1;
  }

  td {
    padding: 10px 10px 10px 20px;

    &.list-item-checkbox {
      padding-left: 5px;
    }
  }

  &-status {
    text-transform: capitalize;
  }

  .available {
    position: relative;

    &:before {
      content: '';
      background-color: #00FF00;
      border-radius: 100%;
      height: 10px;
      left: 5px;
      margin-top: -5px;
      position: absolute;
      top: 50%;
      width: 10px;
    }
  }
}
</style>
