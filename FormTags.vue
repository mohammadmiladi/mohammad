<template>
  <div class="d-flex flex-row justify-content-start align-items-center" v-if="flag">
    <div class="FormTagsTitle">{{ title }}</div>
    <div class="d-flex align-items-center justify-content-start overflow-auto">
      <div class="tagDiv d-flex justify-content-around align-items-center" v-for="(tag, index) in tags" :key="tag.id">
        <span>
          {{ tag.title }}
        </span>
        <i class="fa fa-times" @click="updateTag('remove', tag.id, index)"></i>
      </div>
      <form @submit.prevent="updateTag('add', FormTagData.newTag)" class="addTagForm" v-if="tags.length < tagsLength">
        <div class="d-flex flex-row">
          <input v-model="FormTagData.newTag" type="text" class="tagDivInput" :placeholder="FormTagData.newTagPlaceholder" :maxlength="maxlength" />
          <i @click="updateTag('add', FormTagData.newTag)" class="fa fa-plus" role="button" type="submit"></i>
        </div>
      </form>
    </div>
    <Toast title="لطفا زبان کیبورد خود را فارسی کنید." error :showToast="showToast" />
  </div>
</template>

<script>
import Toast from '../../Toast.vue'
export default {
  components: {
    Toast,
  },
  props: {
    tags: {
      type: Array,
    },
    isAddUser: {
      type: Boolean,
      default: false,
    },
    title: {
      type: String,
      default: 'تگ ها',
    },
    maxlength: {
      type: Number,
      default: 10,
    },
    tagsLength: {
      type: Number,
      default: 4,
    },
  },
  data() {
    return {
      showToast: false,
      flag: true,
      FormTagData: {
        newTagPlaceholder: 'اضافه کردن تگ',
        newTag: '',
        tags: [],
      },
    }
  },

  methods: {
    updateTag(status, data, index) {
      const userId = this.$route.params.id
      if (status == 'add' && this.FormTagData.newTag != '') {
        if (this.justPersian(data)) {
          if (this.isAddUser) {
            this.$emit('tagListUpdate', [this.FormTagData.newTag])
          } else {
            this.$emit('updateTag', { userId: userId, title: data })
          }
          this.FormTagData.newTag = ''
          this.FormTagData.newTagPlaceholder = 'اضافه کردن تگ'
        }
      } else if (status == 'remove') {
        if (this.isAddUser) {
          this.FormTagData.tags = this.tags.splice(index, 1)
          this.$emit('tagListUpdate', this.FormTagData.tags[0])
        } else {
          this.$emit('updateTag', { userId: userId, tagId: data })
        }
      }
    },
    justPersian(str) {
      var p = /^[\u0600-\u06FF\s]+$/
      if (!p.test(str)) {
        this.showToast = true
        setTimeout(() => {
          this.showToast = false
        }, 1500)
        return
      } else {
        return true
      }
    },
  },
}
</script>

<style scoped>
::-webkit-scrollbar {
  display: none;
}
.FormTagsTitle {
  color: #495057;
  margin-left: 5px;
}
.addTagForm {
  width: 130px;
}
.tagDivInput::placeholder {
  color: #ced4da;
}
::v-deep .alert {
  padding: 0 !important;
}
.tagDivInput {
  padding: 3px !important;
  text-align: right;
  border-color: #ced4da !important;
  border: 1px solid;
  border-radius: 6px;
  background-color: #fff;
  text-align: right;
  -ms-flex-item-align: center;
  align-self: center;
  font-size: 13px;
  font-weight: 400;
  direction: rtl;
  max-width: 120px;
  color: #556ee6;
}
.tagDivInput::placeholder {
  font-size: 11px !important;
  padding: 3px !important;
}

input.tagDivInput:focus-visible {
  outline: 0 !important;
}
.tagDivInput ~ i {
  left: 25px;
  border-radius: 5px 0 0 5px;
  position: relative;
  background-color: #556ee6;
  -ms-flex-item-align: center;
  align-self: center;
  color: #fff;
  font-size: 11px;
  padding: 7px 8px;
  top: 0px;
  cursor: pointer;
}

.tagDiv {
  border: 1px solid;
  border-radius: 6px;
  border-color: #556ee6;
  background-color: #fff;
  text-align: center;
  padding: 2px 3px;
  font-size: 13px;
  font-weight: 400;
  direction: rtl;
  margin-left: 8px;
  white-space: nowrap;
  color: #556ee6;
}
.tagDiv i {
  cursor: pointer;
  margin: 0 3px 0 0;
  font-size: 10px;
}
</style>
