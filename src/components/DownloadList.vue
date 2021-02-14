<template>
  <table class="download-list">
    <tr class="download-list__actions">
      <th><input class="master-select center" type="checkbox" ref="masterSelect"  @click="handleMasterSelection"/></th>
      <th>{{ numberSelected }}</th>
      <th><div class="download-selected center" @click="handleDownloadFiles">Download Selected</div></th>
    </tr>

    <tr class="download-list__header">
      <td></td>
      <td>Name</td>
      <td>Device</td>
      <td>Path</td>
      <td>Status</td>
    </tr>

    <!-- could I simply pass an ischecked prop and handle the selection of the check that way -->
    <selectable-list-item
      class="download-list__item"
      v-for="(row, idx) in rowInfo"
      :key="idx"
      :rowData="row"
      :handleSelectRow="selectRow"
      :ref="row.name"
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
  data: () => {
    return {
      selectedItems: [],
      rowInfo: downloadData,
    };
  },
  computed: {
    numberSelected() {
      return `
        ${this.selectedItems.length === 0 ? 'None ' : ''}
        Selected
        ${this.selectedItems.length > 0 ? this.selectedItems.length : ''}
      `;
    },
  },
  methods: {
    selectRow(event, element, rowData) {
      event.preventDefault();
      element.checked = !element.checked;
      
      if (element.checked) {
        this.selectedItems.push(rowData);
        element.closest('tr').classList.add('active');
      } else {
        const idx = this.selectedItems.findIndex((row) => row.device === rowData.device);
        this.selectedItems.splice(idx, 1);
        element.closest('tr').classList.remove('active');
      }
      this.updateMasterCheck();
    },
    updateMasterCheck() {
      if (this.selectedItems.length === this.rowInfo.length) {
        this.$refs.masterSelect.checked = true;
        this.$refs.masterSelect.indeterminate = false;
      } else if (this.selectedItems.length < this.rowInfo.length && this.selectedItems.length != 0) {
        this.$refs.masterSelect.indeterminate = true;
      } else if (this.selectedItems.length === 0) {
        this.$refs.masterSelect.checked = false;
        this.$refs.masterSelect.indeterminate = false;
      }
    },
    handleMasterSelection() {
      for (let row of this.rowInfo) {
        let childRow = this.$refs[row.name].$refs[row.name];
        
        if (this.$refs.masterSelect.checked === false) {
          childRow.checked = false;
          childRow.closest('tr').classList.remove('active');
          this.selectedItems = [];
        } else if (this.$refs.masterSelect.checked === true) {
          if (!childRow.checked) {
            childRow.checked = true;
            childRow.closest('tr').classList.add('active');
            this.selectedItems.push(row);
          }
        } 
      }
    },
    handleDownloadFiles() {
      if (this.selectedItems.length === 0) return;
      let alertString = '';

      for (let item of this.selectedItems) {
        if (item.status === 'available') {
          alertString += `${item.device} - ${item.path}\n`;
        }
      }
      
      if (alertString === '') return;
      alert(alertString);
    },
  },
};
</script>

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

.download-selected {
  cursor: pointer;
}
</style>
