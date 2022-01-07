<script>
export default {
  props: {
    title: {
      type: String,
      default: '',
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
  },
  computed: {
    generateNum() {
      return Math.random()
    },
    inputVal: {
      get() {
        return this.value
      },
      set(val) {
        this.$emit('input', val)
      },
    },
  },
  methods: {
    enterClicked() {
      this.$emit('enterButton')
    },
  },
}
</script>

<template>
  <div class="mainInput">
    <div v-if="title != ''" class="labelStyle" :style="title.length > 12 ? { whiteSpace: 'none' } : { whiteSpace: 'nowrap' }">{{ title }}</div>
    <div class="switch">
      <input :disabled="disabled" type="checkbox" v-model="inputVal" :id="generateNum" />
      <label :for="generateNum"></label>
    </div>
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
.switch {
  display: inline-flex;
  width: 56px;
}
.switch input[type='checkbox'] {
  height: 0;
  width: 0;
  visibility: hidden;
}
.switch input[type='checkbox']:checked + label {
  background: #34c38f;
}
.switch input[type='checkbox']:checked + label::after {
  left: calc(100% - 4px);
  transform: translateX(-100%);
}
.switch label {
  cursor: pointer;
  width: 48px;
  height: 24px;
  background: grey;
  display: block;
  border-radius: 24px;
  position: relative;
}
.switch label::after {
  content: '';
  position: absolute;
  top: 4px;
  left: 4px;
  width: 16px;
  height: 16px;
  background: white;
  border-radius: 16px;
  transition: 0.3s;
}
</style>
