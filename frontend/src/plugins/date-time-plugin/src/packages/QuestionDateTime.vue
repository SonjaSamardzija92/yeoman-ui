<template>
  <div class="date-picker-plugin">
    <v-menu
      :close-on-content-click="true"
      :nudge-right="40"
      transition="scale-transition"
      offset-y
      min-width="290px"
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
        ></v-text-field>
      </template>
      <v-date-picker v-model="date" @input="onInput"></v-date-picker>
    </v-menu>
  </div>
</template>

<script>
import { format } from "date-fns";
export default {
  name: "QuestionDateTime",
  props: {
    question: Object,
  },
  data: () => ({
    date: new Date().toISOString().substr(0, 10),
    defaultDateFormat: "dd.MM.yyyy",
    dateFormatted: null,
  }),
  beforeMount() {
    if (this.question.answer) {
      this.date = new Date(this.question.answer).toISOString().substr(0, 10);
    }
  },
  watch: {
    date() {
      this.dateFormatted = this.formatDate(this.date);
    },
  },
  methods: {
    onInput(answer) {
      if (answer !== undefined) {
        this.$emit("answerChanged", this.question.name, answer);
      }
    },

    formatDate(date) {
      if (!date) return null;

      return format(
        new Date(date),
        (this.question.guiOptions &&
          this.question.guiOptions.format &&
          this.question.guiOptions.format.dateFormat) ||
          this.defaultDateFormat
      );
    },
  },
};
</script>
<style>
.date-picker-plugin .v-input__prepend-outer {
  position: absolute !important;
  right: 0 !important;
  margin-right: 0px !important;
}
.date-picker-plugin .v-input__control {
  margin-right: 30px !important;
}
.v-application .v-picker__title.primary {
  background-color: var(--vscode-sideBar-background) !important;
  color: var(--vscode-foreground) !important;
}
.v-application .v-date-picker-table__current.accent {
  background-color: var(--vscode-sideBar-background) !important;
  color: var(--vscode-foreground) !important;
}

.v-application .date-picker-plugin .v-icon.primary--text {
  color: var(--vscode-foreground) !important;
  caret-color: var(--vscode-foreground) !important;
}
</style> 
