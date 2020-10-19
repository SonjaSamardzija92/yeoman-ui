
<template>
  <div>
    <v-select
      :items="items"
      v-model="select"
      v-on:input="onChange(`${select}`)"
      :disabled="readonly"
    ></v-select>
  </div>
</template>

<script>
import Vue from "vue";

export default Vue.extend({
  data() {
    return {
      select: "",
      items: [],
      readonly: false,
    };
  },

  beforeMount() {
    this.items = this.params.colDef.enum ? this.params.colDef.enum : [];
    this.select = this.params.node.data[this.params.colDef.field];
    this.readonly = this.params.colDef.readonly;
  },

  methods: {
    onChange(select) {
      this.params.setValue(select);
      this.params.context.componentParent.answerChanged();
      this.params.api.stopEditing();
    },
  },
});
</script> 
 
<style>
.v-select.v-input {
  padding-top: 0px !important;
}

.v-select.v-input div.v-input__control {
  background-color: transparent !important;
}
.v-select__selection {
  padding-left: 5px;
}
</style> 
