<template>
<div class="date-picker-editor">
  <date-picker
    v-model="dateFormatted"
    value-type="YYYY-MM-DD"
    :format="defaultDateFormat"
    @change="handleChange"
  ></date-picker>
    </div>
</template>

<script>
import DatePicker from "vue2-datepicker";
import "vue2-datepicker/index.css";

export default {
  name: "QuestionDateTime",
  props: {
    question: Object,
  },
  components: { DatePicker },
  data: () => ({
    defaultDateFormat: null,
    dateFormatted: null,
  }),
  beforeMount() {
    if (this.question.answer) {
      this.dateFormatted = new Date(this.question.answer)
        .toISOString()
        .substr(0, 10);
    }
    this.defaultDateFormat =
      (this.question.guiOptions &&
        this.question.guiOptions.format &&
        this.question.guiOptions.format.dateFormat) ||
      "DD/MM/YYYY";
  },
  methods: {
    handleChange(value) {
      if (value !== undefined) {
        this.$emit("answerChanged", this.question.name, value);
      }
    },
  },
};
</script>
