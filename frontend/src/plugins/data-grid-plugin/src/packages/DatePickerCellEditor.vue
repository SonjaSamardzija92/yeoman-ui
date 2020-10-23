
<template>
  <date-picker
    v-model="dateFormatted"
    value-type="YYYY-MM-DD"
    :format="defaultDateFormat"
    @change="handleChange"
    :disabled="readonly"
  ></date-picker>
</template>
<script>
import Vue from "vue";
import DatePicker from "vue2-datepicker";
import "vue2-datepicker/index.css";

export default Vue.extend({
  components: { DatePicker },
  data() {
    return {
      defaultDateFormat: null,
       readonly: false,
      dateFormatted: null,
    };
  },
  beforeMount() {
    if (this.params.value) {
      this.dateFormatted = new Date(this.params.value)
        .toISOString()
        .substr(0, 10);
    }
    this.readonly = this.params.colDef.readonly;
    this.defaultDateFormat =
      (this.params.colDef.format && this.params.colDef.format.formatString) ||
      "DD/MM/YYYY";
  },
  methods: {
    handleChange(answer) {
      if (answer !== undefined) {
        this.params.setValue(answer);
        this.params.context.componentParent.answerChanged();
      }
    },
  },
});
</script> 
