<template>
  <tr class="list-item">
    <td class="list-item-checkbox">
      <input type="checkbox" v-bind:name="rowData.name" />
    </td>
    <td class="list-item-name">{{ rowData.name }}</td>
    <td class="list-item-device">{{ rowData.device }}</td>
    <td class="list-item-path">{{ rowData.path }}</td>
    <td class="list-item-status">{{ rowData.status }}</td>
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
  },
  methods: {},
};
</script>

<style scoped lang="scss">
.list-item {
  cursor: pointer;

  &:hover {
    background-color: #f2efef;
  }

  td {
    padding: 10px 10px 10px 20px;

    &.list-item-checkbox {
      padding-left: 0;
    }
  }

  &-status {
    text-transform: capitalize;
  }
}
</style>
