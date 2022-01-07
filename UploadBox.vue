<script>
import fileMannager from '../../../../services/file'
export default {
  name: 'UploadBox',
  props: {
    title: {
      type: String,
      default: '',
    },
    placeholder: {
      type: String,
      default: '',
    },
    componentData: {
      type: Array,
      default: null,
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
    filesLength: {
      type: Number,
      default: 0,
    },
  },
  data() {
    return {
      files: [],
      fileUpload: '',
    }
  },
  created() {
    if (this.componentData.length > 0) {
      this.files = this.componentData
    }
  },
  methods: {
    handleFileUpload() {
      this.fileUpload = this.$refs.fileInput.files[0]
      
      let formData = new FormData()
      formData.append('ListFiles', this.fileUpload)
      fileMannager
        .addFile(formData)
        .then((res) => {
          if (this.filesLength === 0) {
            this.files.push({
              url: res.data.data[0].downloadUrl,
              type: this.fileUpload.type,
              name: this.fileUpload.name,
            })
          } else if (this.files.length < this.filesLength) {
            this.files.push({
              url: res.data.data[0].downloadUrl,
              type: this.fileUpload.type,
              name: this.fileUpload.name,
            })
          } else {
            return
          }
        })
        .then(() => {
          this.$emit('fileBoxData', this.files)
        })
    },
    deleteImage(file) {
      this.files.splice(file, 1)
      this.$emit('fileBoxData', this.files)
    },
    resetImageUploader() {
      this.$refs.fileInput.value = ''
    },
  }
}
</script>

<template>
  <div>
    <div class="mainInput">
      <div v-if="title != ''" :class="labelClass" class="labelStyle" :style="title.length > 12 ? `whiteSpace: nowrap; font-size: 12px` : { whiteSpace: 'none' }">{{ title }}</div>
      <div class="w-100">
        <div :class="files.length == 0 ? ' uploadFile' : 'uploadFile-withFiles'">
          <div v-if="files.length === 0" class="child w-100 h-100" @click="$refs.fileInput.click()">
            <div>
              <img src="@/assets/images/teamtask/upload-icon.svg" />
            </div>
            <div>عکس یا فایل خود را انتخاب کنید</div>
          </div>
          <div class="h-100 px-2 py-1 box-style row gx-2" v-else>
            <div v-for="(file, index) in files" :key="file.id" class="position-relative image col-3 text-center my-1">
              <!-- <a :href="file.url" class="d-block"> -->
              <div class="image-class" v-if="file.type == 'image/png' || file.type == 'image/jpg' || file.type == 'image/jpeg'">
                <img class="img-responsive w-100" height="140" :src="file.url" alt="" />
              </div>
              <div class="file-class" v-else-if="file.type == 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'">
                <i style="font-size: 50pt; color: green" class="fa-thin fa-file-excel"></i>
                <div data-bs-toggle="tooltip" :title="file.name" class="ellipsis">
                  {{ file.name }}
                </div>
              </div>
              <div class="file-class" v-else-if="file.type == 'application/pdf'">
                <i style="font-size: 50pt; color: red" class="fa-thin fa-file-pdf"></i>
                <div data-bs-toggle="tooltip" :title="file.name" class="ellipsis">
                  {{ file.name }}
                </div>
              </div>
              <div class="file-class" v-else-if="file.type == 'application/vnd.openxmlformats-officedocument.wordprocessingml.document'">
                <i style="font-size: 50pt; color: blue" class="fa-thin fa-file-word"></i>
                <div data-bs-toggle="tooltip" :title="file.name" class="ellipsis">
                  {{ file.name }}
                </div>
              </div>
              <div class="file-class" v-else-if="file.type == 'application/x-zip-compressed'">
                <i style="font-size: 50pt; color: black" class="fa-thin fa-file-archive"></i>
                <div data-bs-toggle="tooltip" :title="file.name" class="ellipsis">
                  {{ file.name }}
                </div>
              </div>
              <div v-else>{{ file.name }}</div>
              <!-- </a> -->
              <div class="remove-icon" @click="deleteImage(index)">
                <i class="fa fa-times"></i>
              </div>
            </div>
            <div v-if="filesLength === 0 " class="col-3" @click="$refs.fileInput.click()">
              <div class="uploaded-photo addNewFile my-1">
                <i class="fa fa-plus"></i>
              </div>
            </div>
            <div v-else-if="files.length < filesLength" class="col-3" @click="$refs.fileInput.click()">
              <div class="uploaded-photo addNewFile my-1">
                <i class="fa fa-plus"></i>
              </div>
            </div>
          </div>
          <input class="d-none" type="file" ref="fileInput" @click="resetImageUploader" @change="handleFileUpload()" />
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.ellipsis {
  position: relative;
  white-space: nowrap;
  overflow: hidden;
  max-width: 100%;
  text-overflow: ellipsis;
}
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
.file-class {
  color: black;
  text-align: center;
  height: 164px;
  direction: ltr;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  border: 2px solid #ced4da;
  border-radius: 5px;
  padding: 10px;
}
.image-class {
  border: 2px solid #ced4da;
  border-radius: 5px;
  padding: 10px;
}
.addNewFile {
  display: flex;
  align-items: center;
  justify-content: center;
  border: 2px dashed #ced4da;
  cursor: pointer;
  height: 163px;
  i {
    font-size: 45px;
    text-align: center;
    display: block;
    color: #ced4da;
  }
}
.box-style {
}
.remove-icon {
  position: absolute;
  top: 7px;
  left: 20px;
  background-color: gray;
  border-radius: 50%;
  width: 20px;
  height: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  i {
    color: white;
    // font-size: 20px;
  }
}
.uploadText {
  margin-right: 8px;
  font-style: normal;
  font-weight: normal;
  font-size: 14px;
  line-height: 23px;
  text-align: center;
  color: #999999;
}
.uploadFile {
  width: 100%;
  height: 100%;
  z-index: 20;
  display: flex;
  min-height: 250px;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background: #ffffff;
  border: 2px dashed #ced4da;
  box-sizing: border-box;
  border-radius: 4px;
}
.uploadFile-withFiles {
  height: auto;
  overflow-x: hidden;
  min-height: 250px;
  overflow-y: auto;
  background: #ffffff;
  border: 2px dashed #ced4da;
  box-sizing: border-box;
  border-radius: 4px;
}

.image {
  z-index: 50 !important;
}
.x {
  position: absolute;
  top: 0%;
  left: 5%;
  cursor: pointer;
}

.child {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  cursor: pointer;
}
</style>
