<template>
  <div class="custom-tooltip">
    <p>
      <span>{{ tooltipText }}</span>
    </p>
  </div>
</template>

<script>
import Vue from "vue";

export default Vue.extend({
  data() {
    return {
      tooltipText: "qqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqqq",
    };
  },

  beforeMount() {
    var params = this.params,
      isHeader = params.rowIndex === undefined,
      isGroupedHeader = !!params.colDef.children; // ,
    // valueToDisplay = params.value.value ? params.value.value : "- Missing -";

    this.setState({
      tooltipText: "aaaaaaaaaaaaaaaaaaaa", // valueToDisplay,
      isHeader: isHeader,
      isGroupedHeader: isGroupedHeader,
    });
  },

  methods: {
    setState(obj) {
      const that = this;

      Object.keys(obj).forEach(function (key) {
        that[key] = obj[key];
      });
    },
  },
});
</script>

<style>
.custom-tooltip {
  position: absolute;
  width: 150px;
  height: 70px;
  border: 1px solid cornflowerblue;
  overflow: hidden;
  pointer-events: none;
  transition: opacity 1s;
  background-color: white;
}

.custom-tooltip.ag-tooltip-hiding {
  opacity: 0;
}

.custom-tooltip p {
  margin: 5px;
  white-space: nowrap;
}

.custom-tooltip p:first-of-type {
  font-weight: bold;
}
</style>