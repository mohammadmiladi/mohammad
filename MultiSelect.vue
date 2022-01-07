<script>
export default {
  props: {
    title: {
      type: String,
      default: '',
    },
    placeholder: {
      type: String,
      default: '',
    },
    inputType: {
      type: String,
      default: 'text',
    },
    isPrice: {
      default: false,
      type: Boolean,
    },
    minlength: {
      type: Number,
    },
    maxlength: {
      type: Number,
      default: 50,
    },
    id: {
      type: String,
    },
    disable: {
      type: Boolean,
      default: false,
    },
    value: {
      default: null,
    },
    componentData: {
      type: Array,
      default: null,
    },
  },
  computed: {
    inputVal: {
      get() {
        return this.value
      },
      set(val) {
        this.$emit('input', val)
      },
    },
  },
  mounted() {
  },
}
</script>

<template>
  <div class="mainInput">
    <div class="labelStyle" :style="title.length > 12 ? { whiteSpace: 'none' } : { whiteSpace: 'nowrap' }">{{ title }}</div>
    <b-form-checkbox-group v-model="inputVal" :options="componentData" value-field="key" text-field="value" :id="inputVal.key" :name="inputVal.key"></b-form-checkbox-group>
  </div>
</template>

<style lang="scss" scoped>
.mainInput {
  display: flex;
  align-items: flex-start;
  flex-direction: column;
}

.labelStyle {
  margin-bottom: 20px;
  margin-left: 10px;
  display: table;
  font-style: normal;
  font-weight: normal;
  font-size: 13px;
  color: #444444;
  justify-content: flex-start;
  align-items: center;
}

/* Chrome, Safari, Edge, Opera */
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

/* Firefox */
input[type='number'] {
  -moz-appearance: textfield;
}
</style>
