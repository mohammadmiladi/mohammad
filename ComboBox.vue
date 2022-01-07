<template>
  <div class="mainInput">
    <div v-if="title" class="labelStyle" :style="title.length > 12 ? { whiteSpace: 'none' } : { whiteSpace: 'nowrap' }">{{ title }}</div>
    <div style="position: relative;width: 100%;">
      <input
        :id="comboBoxID"
        :type="type"
        autocomplete="off"
        :class="showError ? `errorStyle` : ``"
        :style="required ? 'border-right: 5px solid #fe5e61;' : inputStyle"
        :required="required"
        v-model="myValue"
        @keyup="searchItem"
        :maxlength="maxlength"
        @keydown.self.enter.prevent="selectItemWithEnter(selectedIndex)"
        @keydown.40.exact="selectNext"
        @keydown.38.exact="selectPrev"
        :disabled="disabled"
        :placeholder="placeholder"
        class="input__style"
        @focus="openItems"
        @blur="closeItems"
      />
      <i :class="{ down: isToggled, up: !isToggled }"> </i>
      <div v-if="showError" class="errorClass">{{ errortext }}</div>
      <div @scroll="onScroll" :class="ShowItems ? 'itemStyle' : 'd-none'" ref="scrollCombo">
        <div v-if="searchedData.length > 0">
          <div
            @click="selectItem(data)"
            v-for="(data, index) in searchedData"
            class="itemRow"
            :style="index == selectedIndex ? ` background-color: #f2f4f7;` : ''"
            :key="data.id"
            :class="isColor ? 'd-flex align-items-center ps-3' : ''"
          >
            <div v-if="isColor && data.code" :style="isColor ? `background: ${data.code}` : ''" class="color__preview"></div>
            <div>{{ data.value }}</div>
          </div>
        </div>
        <div class="mt-2" v-else>دیتایی موجود نمی باشد.</div>
        <div v-if="hasButton" class="buttonClass" style="border-top: 1px solid #ced4da" @click="buttonAction">
          <p class="p-2 m-0 p">{{ buttonText }}</p>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import { searchComboMixins } from '../../../../utils/mixins'
export default {
  name: 'comboBox-input',
  data() {
    return {
      ShowItems: false,
      isToggled: false,
      myValue: '',
      selectedIndex: -1,
    }
  },
  mixins: [searchComboMixins],
  props: {
    mainClass: {
      type: String,
      default: '',
    },
    title: {
      type: String,
      default: '',
    },
    name: {
      type: String,
      default: '',
    },
    type: {
      type: String,
      default: 'text',
    },
    placeholder: {
      type: String,
      default: '',
    },
    inputClassName: {
      type: String,
      default: '',
    },
    inputStyle: {
      type: String,
      default: '',
    },
    labelStyle: {
      type: String,
      default: 'right:5%;',
    },
    showError: {
      type: Boolean,
      default: false,
    },
    errortext: {
      type: String,
      default: '',
    },
    required: {
      type: Boolean,
      default: false,
    },
    selectedValue: {
      type: String,
      default: '',
    },
    maxlength: {
      type: Number,
    },
    componentData: {
      type: Array,
      default: null,
    },
    searchedData: {
      type: Array,
      default: null,
    },
    disabled: {
      type: Boolean,
      default: false,
    },
    hasButton: {
      type: Boolean,
      default: false,
    },
    buttonText: {
      type: String,
      default: '',
    },
    pagination: {
      type: Boolean,
      default: false,
    },
    invoice: {
      type: Boolean,
      default: false,
    },
    isColor: {
      type: Boolean,
      default: false,
    },
  },
  watch: {
    selectedValue(val) {
      this.myValue = val
    },
    searchedData() {
      this.selectedIndex = -1
    },
    ShowItems(val) {
      if (val == true) {
        this.$emit('itemsOpened', true)
      } else if (val == false) {
        this.$emit('itemsOpened', false)
      }
    },
  },
  methods: {
    selectNext() {
      this.selectedIndex++
    },
    selectPrev() {
      this.selectedIndex--
    },
    selectItemWithEnter(enterIndex) {
      this.selectItem(this.searchedData[enterIndex])
      document.getElementById(this.comboBoxID).blur()
    },
    onScroll({ target: { scrollTop, clientHeight, scrollHeight } }) {
      if (scrollTop + clientHeight >= scrollHeight) {
        if (this.pagination) {
          this.$emit('loadMoreData')
        }
      }
    },
    openItems() {
      this.ShowItems = true
      this.isToggled = true
    },
    closeItems() {
      setTimeout(() => {
        this.ShowItems = false
        this.isToggled = false
      }, 250)
    },
    searchItem(searchedValue) {
      var searchValue = searchedValue.key
      if (searchValue == 'ArrowLeft' || searchValue == 'ArrowRight' || searchValue == 'ArrowUp' || searchValue == 'ArrowDown') {
        return
      } else {
        this.$refs.scrollCombo.scrollTop = 0
        this.$emit('searchedItem', this.myValue)
      }
    },
    selectItem(data) {
      this.myValue = data.value
      this.$emit('selectedItem', data)
    },
    buttonAction() {
      this.$emit('buttonAction')
    },
  },
  computed: {
    inputVal: {
      get() {
        return this.value
      },
    },
    comboBoxID() {
      return Math.random()
    },
  },
  mounted() {
    if (this.invoice) {
      this.myValue = this.selectedCustomer.value
    }
  },
  created() {
    if (this.selectedValue) {
      this.myValue = this.selectedValue
    }
  },
}
</script>

<style scoped lang="scss">
.errorStyle {
  line-height: 17px;
  width: 100%;
  color: #74788d;
  background: #ffffff;
  border: 1px solid #fe5e61;
  padding: 6px 10px;
  box-sizing: border-box;
}
.color__preview {
  background: white;
  width: 12px;
  height: 12px;
  border-radius: 1px;
  margin-left: 10px;
}
.errorClass {
  font-size: 10px;
  color: #fe5e61;
}
.buttonClass {
  position: sticky;
  bottom: 0;
  background: white;
  border-top: 1px solid #ced4da;
  cursor: pointer;
  font-size: 13px;
  font-style: normal;
  font-weight: 400;
  line-height: 21px;
  letter-spacing: 0px;
  transition: 0.2s ease-in-out;
}
.buttonClass:hover {
  background-color: #f2f4f7;
}
.mainInput {
  display: flex;
  align-items: center;
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

i {
  position: absolute;
  top: 10px;
  left: 10px;
  border: solid #555555;
  border-width: 0 2px 2px 0;
  display: inline-block;
  padding: 2px;
}
.down {
  transform: rotate(-135deg);
}
.up {
  transform: rotate(45deg);
}
.mainClass {
  width: 100%;
  position: relative;
}
.loading-container {
  border-radius: 8px;
  display: flex;
  margin-bottom: 20px;
  align-items: center;
  justify-content: center;
  flex-direction: column;
}
::-webkit-scrollbar {
  width: 5px;
  display: none;
}
::-webkit-scrollbar-track {
  box-shadow: inset 0 0 5px grey;
  border-radius: 10px;
}
::-webkit-scrollbar-thumb {
  border-radius: 5px;
  background: #556ee6;
}

input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

/* Firefox */
input[type='number'] {
  -moz-appearance: textfield;
}
input {
  -webkit-appearance: none;
  appearance: none;
  border-radius: 5px;
  border: 1px solid #ced4da;
  outline: none;
  padding: 5px 10px;
  text-align: right;
  width: 100%;
}
input:disabled {
  width: 100%;
  color: #c6cbd0;
  background: #ffffff;
  border: 1px solid #ced4da;
  padding: 6px 10px;
  box-sizing: border-box;
}
input:disabled::placeholder {
  color: #c6cbd0;
}
input:disabled + i {
  position: absolute;
  top: 10px;
  left: 10px;
  border: solid #c6cbd0;
  border-width: 0 2px 2px 0;
  display: inline-block;
  padding: 2px;
}
.custom-input {
  width: 100%;
  position: relative;
  font-size: 14px;
  font-weight: 400;
  font-family: IRANSans;
}
.errText {
  color: #eb4137;
  font-size: 12px;
  margin-right: 10px;
  width: 100%;
  text-align: right;
}
.submitStyle2 {
  padding: 7px 23px;
  align-items: center;
  color: #fff !important;
  margin: 10px;
  width: 95%;
  font-size: 15px;
  font-weight: 500;
  box-shadow: 0 2px 7px 0 rgb(214, 214, 214);
  background-color: #2585ff;
}
.blueIR {
  color: #4285f2;
}
.modalData {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.itemRow {
  display: flex;
  min-height: 40px;
  align-items: center;
  color: rgba(0, 0, 0, 0.87) !important;
  outline: none;
  cursor: pointer;
  font-size: 13px;
  padding: 8px 16px;
}
.itemRow:hover {
  background: #ececec;
}
.itemStyle {
  display: block !important;
  width: 100%;
  position: absolute;
  z-index: 2;
  max-height: 200px;
  overflow-x: hidden;
  overflow-y: scroll;
  box-shadow: 0px 5px 5px -3px rgb(0 0 0 / 20%), 0px 8px 10px 1px rgb(0 0 0 / 14%), 0px 3px 14px 2px rgb(0 0 0 / 12%);
  background: white;
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
</style>
