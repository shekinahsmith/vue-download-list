<template>
  <table class="download-list">
    <tr class="download-list__actions">
      <th><input class="master-select center" type="checkbox" /></th>
      <th>
        {{ selectedItems === 0 ? "None" : "" }}
        Selected
        {{ selectedItems > 0 ? selectedItems : "" }}
      </th>
      <th><div class="download-selected center">Download Selected</div></th>
    </tr>

    <tr class="download-list__header">
      <td></td>
      <td>Name</td>
      <td>Device</td>
      <td>Path</td>
      <td>Status</td>
    </tr>

    <selectable-list-item
      class="download-list__item"
      v-for="(row, idx) in rowInfo"
      :key="idx"
      :rowData="row"
    />
  </table>
</template>

<script>
import { downloadData } from "../assets/download-data.js";
import SelectableListItem from "./common-ui/SelectableListItem.vue";

export default {
  name: "DownloadList",
  components: {
    SelectableListItem,
  },
  props: {},
  data: () => {
    return {
      selectedItems: 0,
      rowInfo: downloadData,
    };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.download-list {
  border-collapse: collapse;
  font-size: 14px;
  min-width: 790px;
  text-align: left;
  width: 100%;

  th {
    padding-bottom: 16px;
  }

  tr {
    border: 1px solid #d3d3d3;

    &.download-list__actions {
      border: none;
    }
  }

  &__header > td {
    padding: 20px 10px 10px 20px;
  }
}
</style>
