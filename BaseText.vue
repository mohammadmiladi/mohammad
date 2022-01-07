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
    classes: {
      type: String,
      default: '',
    },
    labelClass: {
      type: String,
      default: '',
    },
    disable: {
      type: Boolean,
      default: false,
    },
    value: {
      default: null,
    },
    required: {
      type: Boolean,
      default: false,
    },
    showError: {
      type: Boolean,
      default: false,
    },
    errortext: {
      type: String,
      default: '',
    },
  },
  computed: {
    inputVal: {
      get() {
        return this.value
      },
      set(val) {
        if (val.length > 0) {
          this.showError = false
        }
        this.$emit('input', val)
      },
    },
  },
  methods: {
    enterClicked() {
      this.$emit('enterButton')
    },
    focused() {
      this.$emit('focused')
    },
  },
}
</script>

<template>
  <div>
    <div class="mainInput" :class="classes">
      <div v-if="title != ''" :class="labelClass" class="labelStyle" :style="title.length > 12 ? `whiteSpace: nowrap; font-size: 12px` : { whiteSpace: 'none' }">{{ title }}</div>
      <div class="w-100 position-relative">
        <b-form-input
          :id="id"
          :class="showError ? `errorStyle` : disable ? `disableInput` : `inputStyle`"
          autocomplete="off"
          :maxlength="maxlength"
          @focus="focused()"
          :minlength="minlength"
          oninput="javascript: if (this.type == 'number') { if (this.value.length > this.maxLength) this.value = this.value.slice(0, this.maxLength);}"
          v-model="inputVal"
          :type="inputType"
          @change="enterClicked"
          :placeholder="placeholder"
          :required="required"
          :disabled="disable"
          :style="required && 'border-right: 5px solid #fe5e61;'"
        ></b-form-input>
        <div v-if="showError" class="errorClass">{{ errortext }}</div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.mainInput {
  display: flex;
  align-items: center;
}
.inputStyle {
  width: 100%;
  color: #74788d;
  background: #ffffff;
  border: 1px solid #ced4da;
  padding: 6px 10px;
  box-sizing: border-box;
}
.disableInput {
  width: 100%;
  color: #c6cbd0;
  background: #ffffff;
  border: 1px solid #ced4da;
  padding: 6px 10px;
  box-sizing: border-box;
}
.errorStyle {
  line-height: 17px;
  width: 100%;
  color: #74788d;
  background: #ffffff;
  border: 1px solid #fe5e61;
  padding: 6px 10px;
  box-sizing: border-box;
}
.errorClass {
  position: absolute;
  bottom: -16px;
  font-size: 10px;
  color: #fe5e61;
}
input:focus {
  outline: none;
}
input:focus::placeholder {
  color: transparent;
}
input::placeholder {
  font-style: normal;
  font-weight: normal;
  font-size: 12px;
  line-height: 20px;
  text-align: right;
  color: #74788d;
}
.labelStyle {
  min-width: 90px;
  max-width: 90px;
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
