
<template>
  <div>
    <ag-grid-vue
      class="ag-theme-alpine"
      :columnDefs="columnDefs"
      :rowData="rowData"
      :defaultColDef="defaultColDef"
      :context="gridContext"
      :frameworkComponents="frameworkComponents"
      :gridOptions="gridOptions"
      @grid-ready="onGridReady"
      @cell-value-changed="handleCellValueChanged"
      :singleClickEdit="true"
      :tooltipShowDelay="tooltipShowDelay"
    >
    </ag-grid-vue>

    <v-btn @click="addNewRow()">Add</v-btn>
  </div>
</template>

<script>
import "ag-grid-community/dist/styles/ag-grid.css";
import "ag-grid-community/dist/styles/ag-theme-alpine.css";
// import { ClientSideRowModelModule } from "@ag-grid-community/client-side-row-model";
import { AgGridVue } from "ag-grid-vue";
import DataGridButtons from "./DataGridButtons";
import DropdownCellEditor from "./DropdownCellEditor";

export default {
  name: "DataGrid",
  props: {
    question: Object,
  },
  data: () => {
    return {
      gridOptions: null,
      columnDefs: null,
      rowData: null,
      columnPromisesData: [],
    };
  },
  components: {
    AgGridVue,
  },

  beforeMount() {
    this.initGrid();
  },

  mounted: function () {
    this.rowData = this.question.answer || [];
  },

  methods: {
    onGridReady(params) {
      this.gridApi = params.api;
      this.columnApi = params.columnApi;
    },

    async initGrid() {
      this.gridOptions = {};
      this.gridContext = { componentParent: this };
      this.frameworkComponents = {
        dataGridButtons: DataGridButtons,
        dropdownCellEditor: DropdownCellEditor,
      };
      this.tooltipShowDelay = 0;

      this.defaultColDef = {
        editable: true,
      };

      this.columnDefs = [];
      if (
        this.question.guiOptions &&
        this.question.guiOptions.columns &&
        Array.isArray(this.question.guiOptions.columns)
      ) {
        try {
          await this.createGridColumns();
        } catch (err) {
          console.log(err);
        }
      }
    },

    async createGridColumns() {
      for (
        let index = 0;
        index < this.question.guiOptions.columns.length;
        index++
      ) {
        const col = this.question.guiOptions.columns[index];

        if (
          col.enumProvider &&
          typeof this.question[col.enumProvider] === "function"
        ) {
          this.columnPromisesData.push({
            promise: this.createColumnPromise(),
            index,
            column: col,
          });
        }
      }

      if (this.columnPromisesData.length > 0) {
        const allPromisses = Promise.all(
          this.columnPromisesData.map((pd) => pd.promise)
        );

        setTimeout(() => {
          for (const pd of this.columnPromisesData) {
            this.$emit(
              "customEvent",
              this.question.name,
              pd.column.enumProvider,
              this.enumProviderCallback,
              pd.column,
              pd.index
            );
          }
        }, 0);

        await allPromisses;
      }

      this.question.guiOptions.columns.forEach((col) => {
        this.columnDefs.push({
          headerName: col.header,
          headerTooltip: col.header,
          field: col.field,
          editable: this.getEditable(col),
          readonly: col.editable !== undefined && !col.editable,
          cellRendererFramework: col.enum ? DropdownCellEditor : undefined,
          enum: col.enum,
          flex: col.width === undefined && 1,
          width: col.width,
        });
      });

      this.columnDefs.push({
        field: "",
        pinned: "right",
        cellRendererFramework: DataGridButtons,
        width: 50,
        editable: false,
         headerTooltip: 'test',
      });
    },

    createColumnPromise(index) {
      let res, rej;

      let promise = new Promise((resolve, reject) => {
        res = resolve;
        rej = reject;
      });

      promise.resolve = res;
      promise.reject = rej;
      promise.index = index;

      return promise;
    },

    enumProviderCallback(result) {
      const { index, data } = result;

      const promiseToResolve = this.columnPromisesData.find(
        (pd) => pd.index === index
      );
      if (promiseToResolve) {
        promiseToResolve.promise.resolve();
        this.question.guiOptions.columns[index].enum = data;
      }
    },

    handleCellValueChanged() {
      this.answerChanged();
    },

    getEditable(col) {
      if (
        col.enum ||
        (col.enumProvider &&
          typeof this.question[col.enumProvider] === "function")
      ) {
        return false;
      } else {
        return col.editable !== undefined ? col.editable : true;
      }
    },

    addNewRow() {
      this.gridApi.applyTransaction({ add: [this.createNewRowData()] });
      this.answerChanged();
    },

    deleteSelectedRow(selectedData) {
      this.gridApi.applyTransaction({ remove: selectedData });
      this.answerChanged();
    },
    answerChanged() {
      let items = [];
      this.gridApi.forEachNode(function (node) {
        items.push(node.data);
      });
      this.$emit("answerChanged", this.question.name, items);
    },

    createNewRowData() {
      return {};
    },
  },
};
</script>
<style>
ag-grid-vue {
  width: 100% !important;
}
.ag-theme-alpine {
  height: 300px;
  border: 1px solid var(--vscode-list-hoverBackground) !important;
}
ag-grid-vue .ag-cell {
  font-size: var(--vscode-font-size) !important;
  font-family: var(--vscode-font-family) !important;
}
ag-grid-vue .ag-cell-label-container {
  font-size: var(--vscode-font-size) !important;
  font-weight: var(--vscode-editor-font-weight) !important;
}
ag-grid-vue .ag-row,
.ag-header-row,
.ag-pinned-right-header,
.ag-header-row,
.ag-header-row-column,
.ag-header-cell,
.ag-header,
.ag-root-wrapper,
.ag-cell {
  border: none !important;
}

.ag-header {
  background-color: hsla(0, 0%, 51%, 0.04) !important;
}
.ag-root,
.ag-row {
  background-color: var(--vscode-editor-background) !important;
}

.ag-row:hover {
  background-color: var(--vscode-button-hoverBackground) !important;
}
.ag-header-cell-label,
.ag-cell {
  color: var(--vscode-foreground) !important;
}
.ag-row-hover {
  background-color: var(--vscode-list-hoverBackground) !important;
}

.ag-input-field-input.ag-text-field-input {
  background-color: var(--vscode-input-background) !important;
  color: var(--vscode-input-foreground) !important;
}
</style> 
