<template>
  <div class="main-admin">
    <Header :headerData="headerData" @userData="userData($event)" />
    <div class="admin-dashboard">
      <div class="add-landak">
        <div class="title-register mb-5">فرم فراموشی کلمه عبور</div>
        <div class="d-flex align-items-center w-100 h-100 mt-4">
          <input v-model="mobileData" type="number" placeholder="9121234567" class="input-mobile" />
          <div class="mobile-flag">
            <div class="iran-code">+98</div>
            <img src="@/assets/images/iran.svg" width="24" alt="iran" />
          </div>
        </div>
        <div class="d-flex align-items-center w-100 h-100 mt-4">
          <div @click="togglePassword" class="hide-eye">
            <i v-if="showPass" class="bx bx-show"></i>
            <i v-else class="bx bx-hide"></i>
          </div>
          <input v-model="passwordData" placeholder="کلمه عبور" class="input-password" :type="togglePass" />
        </div>
        <div class="d-flex align-items-center justify-content-center w-100 h-100 mt-5">
          <button @click="sendSms()" class="approveButton">ارسال کد فعالسازی</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Header from '../common/Header.vue'
import { adminDashboardMixins } from '../../utils/mixins'
import dataProvider from '../../services/landak'
import JWT from '../../services/jwt'

export default {
  name: 'add-landak',
  mixins: [adminDashboardMixins],
  components: {
    Header,
  },
  data() {
    return {
      showPass: false,
      togglePass: 'password',
      mobileData: '',
      passwordData: '',
      headerData: {},
    }
  },
  created() {
    if (JWT.getToken()) {
      this.$router.push({ path: '/' })
    }
    dataProvider.getUserInformation().then((res) => {
      this.headerData = res.data
      this.showPage = true
    })
  },
  methods: {
    sendSms() {
      let forgotData = {
        prePhoneNumber: '+98',
        phoneNumber: this.mobileData,
        password: this.passwordData,
      }
      dataProvider.forgotPassword(forgotData).then((res) => {
        window.localStorage.setItem('userID', res.data)
        setTimeout(() => {
          this.$router.push({ path: '/admin/verify-phone-number' })
        }, 500)
      })
    },
    togglePassword() {
      this.showPass = !this.showPass
      if (this.togglePass == 'password') {
        this.togglePass = 'text'
      } else {
        this.togglePass = 'password'
      }
    },
  },
}
</script>

<style scoped lang="scss">
* {
  scroll-behavior: smooth;
}
::-webkit-scrollbar {
  display: none;
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
::v-deep .custom-control-label {
  margin-right: 10px;
  font-size: 14px;
}
::v-deep .custom-checkbox {
  display: flex !important;
  align-items: center !important;
}
.main-admin {
  background-color: #f4f4f4;
  min-height: 100vh;
  overflow-x: hidden;
  overflow-y: scroll;
  padding-bottom: 30px;
}
.admin-dashboard {
  padding: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.add-landak {
  box-shadow: 0px 40px 64px -12px rgba(0, 0, 0, 0.08), 0px 0px 14px -4px rgba(0, 0, 0, 0.05), 0px 32px 48px -8px rgba(0, 0, 0, 0.1);
  border-radius: 12px;
  padding: 25px 35px;
  background-color: white;
  min-height: 400px;
  width: 700px;
}
.login-class {
  margin-right: 10px;
  display: flex;
  font-size: 15px;
  align-items: center;
  justify-content: center;
  color: #5a8dee;
  cursor: pointer;
}
.hide-eye {
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 20px;
  width: 50px;
  height: 40px;
  border-radius: 0 8px 8px 0;
  border: 1px solid #c4c3c3;
  border-left: 0;
  cursor: pointer;
}
.iran-code {
  padding-top: 5px;
  direction: ltr;
  font-size: 14px;
  margin-left: 5px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.mobile-flag {
  background-color: #f0f4f7;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 20px;
  width: 90px;
  height: 40px;
  border-radius: 8px 0 0 8px;
  border: 1px solid #c4c3c3;
  border-right: 0;
}
.approveButton {
  padding: 7px 50px;
  color: white;
  background: #5a8dee;
  border-radius: 6px;
}
.title-register {
  width: 100%;
  font-weight: 600;
  display: flex;
  justify-content: center;
  align-items: center;
}
.input-mobile {
  width: 100%;
  border-radius: 0 8px 8px 0;
  border: none;
  outline: none;
  border: 1px solid #c4c3c3;
  height: 40px;
  outline: 0;
  padding: 0 10px;
  direction: ltr;
  text-align: left;
}
.input-mobile::placeholder {
  font-size: 14px;
}
.input-password::placeholder {
  font-size: 14px;
}
.input-password {
  width: 100%;
  border-radius: 8px 0 0 8px;
  border: none;
  outline: none;
  border: 1px solid #c4c3c3;
  height: 40px;
  outline: 0;
  padding: 0 35px;
  direction: ltr;
  text-align: left;
}
.input-title {
  width: 100%;
  border: 1px solid #c4c3c3;
  border-radius: 6px;
  height: 40px;
  outline: 0;
  padding: 0 35px;
}
.input-title::placeholder {
  font-size: 13px;
  color: gray;
}
.input-title:focus::placeholder {
  color: transparent;
}
.human {
  position: absolute;
  right: 10px;
  top: 10px;
  color: #5a8dee;
  font-size: 20px;
}
@media only screen and (max-width: 1440px) {
}
@media only screen and (max-width: 1024px) {
}
@media only screen and (max-width: 768px) {
}
@media only screen and (max-width: 576px) {
}
</style>
