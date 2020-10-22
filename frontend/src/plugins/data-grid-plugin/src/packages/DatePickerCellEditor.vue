
<template>
  <v-menu
    :close-on-content-click="true"
    :nudge-right="40"
    transition="scale-transition"
    offset-y
    min-width="290px"
  >
    <template v-slot:activator="{ on }">
      <v-text-field
        v-model="date"
        prepend-icon="event"
        readonly
        v-on="on"
        hide-details="auto"
        outlined
        dense
        :disabled="readonly"
      ></v-text-field>
    </template>
    <v-date-picker
      v-model="date"
      @input="onInput"
    ></v-date-picker>
  </v-menu>
</template>
<script>
import Vue from "vue";

export default Vue.extend({
  data() {
    return {
      date: new Date().toISOString().substr(0, 10),
      readonly: false,
    };
  },
  beforeMount() {
    if (this.params.value) {
      this.date = new Date(this.params.value).toISOString().substr(0, 10);
    }
    this.readonly = this.params.colDef.readonly;
  },
  methods: {
    onInput(answer) {
      if (answer !== undefined) {
        this.params.setValue(answer);
        this.params.context.componentParent.answerChanged();
      }
    },
  },
});
</script> 
