<template>
  <table class="download-list">
    <tr class="download-list__actions">
      <th>
        <input 
          @click="handleMasterSelection()"
          class="master-select center"
          type="checkbox" 
          ref="masterSelect"
        /></th>
      <th>{{ numberSelected }}</th>
      <th colspan="3">
        <div 
          @click="handleDownloadFiles"
          class="download-selected center"
        >
          <img
            class ="icon-download"
            src="@/assets/images/icon-download.svg" 
            alt="Download Selcted" 
          />
          Download Selected
        </div>
      </th>
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
      @click="handleSelectRow(row)"
      :key="idx"
      :rowData="row"
    >
      <input
        @click.stop
        @change="handleRowActiveStatus($event, row.name)"
        type="checkbox" 
        :name="row.name" 
        :ref="row.name" 
        :value="row"
        v-model="selectedItems"
      />
    </selectable-list-item>
  </table>
</template>

<script>
import { downloadData } from "@/assets/js/download-data.js";
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
        ${this.noSelectedItems ? 'None ' : ''}
        Selected
        ${this.selectedItems.length > 0 ? this.selectedItems.length : ''}
      `;
    },
    numSelectedEqualNumInData() {
      return this.selectedItems.length === this.rowInfo.length
    },
    numSelectedLessThenNumInData() {
      return this.selectedItems.length < this.rowInfo.length;
    },
    noSelectedItems() {
      return this.selectedItems.length === 0;
    }
  },
  methods: {
    addSelectedRow(row) {
      const input = this.$refs[row.name];
      this.selectedItems.push(row);
      input.closest('tr').classList.add('active');
    },
    removeSelectedRow(row) {
      const input = this.$refs[row.name];
      const idx = this.selectedItems.findIndex((selectedRow) => selectedRow.device === row.device);
      this.selectedItems.splice(idx, 1);
      input.closest('tr').classList.remove('active');
    },
    // on table row click ensure checkbox is checked and
    // add row data to selected items array
    handleSelectRow(rowData) {
      const input = this.$refs[rowData.name];
      input.checked = !input.checked;
      
      if (input.checked) {
        this.addSelectedRow(rowData);
      } else {
        this.removeSelectedRow(rowData);
      }
      this.updateMasterCheck();
    },
    // since the click event from the check bubbles up
    // @click.stop was added in the dom to prevent bubbling
    // which was causing undesred side effects. This ensures
    // the default checked behavior also udpates the master
    // check box and the row active status
    handleRowActiveStatus(event) {
      if (event.target.checked) {
        event.target.closest('tr').classList.add('active');
      } else {
        event.target.closest('tr').classList.remove('active');
      }
      this.updateMasterCheck();
    },
    // HTML checkbox indeterminate state can only be
    // manipulated via JS. This ensures that the correct
    // check box state is visible given the right parameters
    updateMasterCheck() {
      const masterSelect = this.$refs.masterSelect;

      if (this.numSelectedEqualNumInData) {
        masterSelect.checked = true;
        masterSelect.indeterminate = false;
      } else if (
        this.numSelectedLessThenNumInData &&
        this.selectedItems.length != 0
      ) {
        masterSelect.indeterminate = true;
      } else if (this.noSelectedItems) {
        masterSelect.checked = false;
        masterSelect.indeterminate = false;
      }
    },
    // onClick of the "masterSelect" checkbox
    // this handles add/removing rows on click
    handleMasterSelection() {
      const masterSelect = this.$refs.masterSelect;

      if (this.numSelectedEqualNumInData) {
        masterSelect.checked = false;

        for (let row of this.rowInfo) {
          let input = this.$refs[row.name];
          input.checked = false;
          this.removeSelectedRow(row);
        }
      } else if (this.numSelectedLessThenNumInData) {
        for (let row of this.rowInfo) {
          let input = this.$refs[row.name];
          masterSelect.checked = true;

          if (!input.checked) {
            input.checked = true;
            this.addSelectedRow(row);
          }
        }
      }
    },
    handleDownloadFiles() {
      if (this.noSelectedItems) return;
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
  align-items: center;
  cursor: pointer;
  display: flex;

  .icon-download {
    height: 20px;
    width: 20px;
  }
}
</style>
