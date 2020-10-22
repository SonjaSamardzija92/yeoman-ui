
<template >
  <div class="date-picker-cell-editor">
    <v-menu
      :close-on-content-click="true"
      :nudge-right="40"
      transition="scale-transition"
      offset-y
    >
      <template v-slot:activator="{ on }">
        <v-text-field
          v-model="dateFormatted"
          prepend-icon="event"
          readonly
          v-on="on"
          hide-details="auto"
          outlined
          dense
          :disabled="readonly"
        ></v-text-field>
      </template>
      <v-date-picker v-model="date" @input="onInput"></v-date-picker>
    </v-menu>
  </div>
</template>
<script>
import Vue from "vue";
import { format } from "date-fns";
export default Vue.extend({
  data() {
    return {
      date: new Date().toISOString().substr(0, 10),
      readonly: false,
      defaultDateFormat: "dd.MM.yyyy",
      dateFormatted: null,
    };
  },
  beforeMount() {
    if (this.params.value) {
      this.date = new Date(this.params.value).toISOString().substr(0, 10);
    }
    this.readonly = this.params.colDef.readonly;
  },
  watch: {
    date() {
      this.dateFormatted = this.formatDate(this.date);
    },
  },
  methods: {
    onInput(answer) {
      if (answer !== undefined) {
        this.params.setValue(answer);
        this.params.context.componentParent.answerChanged();
      }
    },
    formatDate(date) {
      if (!date) return null;

      return format(
        new Date(date),
        (this.params.colDef.format && this.params.colDef.format.formatString) ||
          this.defaultDateFormat
      );
    },
  },
});
</script> 
<style>
.date-picker-cell-editor .v-input__prepend-outer {
  position: absolute !important;
  right: 0 !important;
  margin-right: 0px !important;
}
.date-picker-cell-editor .v-input__control {
  margin-right: 30px !important;
}
</style> 
