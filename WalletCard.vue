<template>
  <div class="superCardMainDiv">
    <div class="cardStyle">
      <b-row class="no-gutters justify-content-around align-items-center">
        <b-col cols="2" md="3" lg="2" class="pl-0 d-flex justify-content-center align-items-center">
          <div class="d-flex justify-content-center align-items-center flex-column">
            <img :src="bankData.logo" class="logoStyle" />
          </div>
        </b-col>
        <b-col cols="9" md="9" class="d-flex flex-column justify-content-evenly align-items-start">
          <div class="cardInfo">
            <div class="ml-1">ادرس:</div>
            <div v-if="bankData.cryptoAddress" style="font-family: sans-serif">{{ sliceProfileAddress(bankData.cryptoAddress) }}</div>
          </div>
          <div class="cardInfo">
            <div class="ml-1">تگ یا ممو:</div>
            <div v-if="bankData.noTag == true">ندارد</div>
            <div v-if="bankData.noTag == false">دارد</div>
          </div>
          <div class="cardInfoAndCopy w-100">
            <div class="d-flex">
              <div class="ml-1">برچسب:</div>
              <div>{{ bankData.name }}</div>
            </div>
            <div class="d-flex">
              <div class="ml-2">
                <img @click="showSnack(bankData.cryptoAddress)" class="cursorPointer" src="@/assets/images/copy-green.svg" width="20px" />
              </div>
              <div class="ml-1">
                <img @click="openDeletecardModal" class="cursorPointer" src="@/assets/images/delete-red.svg" width="20px" />
              </div>
              <b-modal ref="deleteCard" no-close-on-esc hide-footer centered size="md" hide-header-close cancel-title>
                <template #modal-title>
                  <div>حذف کارت</div>
                </template>
                <div v-if="bankData">
                  <p style="font-size: 14px" class="w-100 text-right">آیا از حذف این کارت اطمینان دارید؟</p>
                  <div>
                    <b-button @click="deleteCard(bankData.id)" class="submitStyle2">بله</b-button>
                    <b-button @click="closeModal" class="editStyle">خیر</b-button>
                  </div>
                </div>
              </b-modal>
            </div>
          </div>
        </b-col>
      </b-row>
    </div>
    <div id="snackbar" class="" ref="snack">کپی شد.</div>
  </div>
</template>

<script>
import ProfileDataProvider from '@/services/profile'

export default {
  props: {
    bankData: {
      default: null,
    },
  },
  methods: {
    showSnack(data) {
      var snack = this.$refs.snack
      var copyText = data
      navigator.clipboard.writeText(copyText)
      snack.classList.add('show')
      setTimeout(() => {
        snack.classList.remove('show')
      }, 1500)
    },
    openDeletecardModal() {
      this.$refs['deleteCard'].show()
    },
    closeModal() {
      this.$refs['deleteCard'].hide()
    },
    deleteCard(id) {
      ProfileDataProvider.deleteWalletAddress(id)
      setTimeout(() => {
        this.$refs['deleteCard'].hide()
        ////////// Show Wallet API Call //////
        ProfileDataProvider.getAddresses()
          .then((res) => {
            this.$store.dispatch('profile/setWalletData', res.data.data)
            console.log(this.walletData)
          })
          .catch((err) => {
            console.log('we have an error :', err)
          })
      }, 300)
    },
    sliceProfileAddress(data) {
      if (data) {
        const a = data
        const b = a.slice(0, 10) + '****' + a.slice(a.length - 10, a.length)
        return b
      }
    },
  },
}
</script>

<style scoped lang="scss">
#snackbar {
  visibility: hidden;
  min-width: 250px;
  margin-left: -125px;
  background-color: #333;
  color: #fff;
  border-radius: 2px;
  position: fixed;
  z-index: 99999999;
  left: 50%;
  bottom: 30px;
  align-items: center;
  background-color: #424242;
  border-radius: 4px;
  pointer-events: auto;
  transition: inherit;
  transition-property: opacity, transform;
  box-shadow: 0 3px 5px -1px rgba(0, 0, 0, 0.2), 0 6px 10px 0 rgba(0, 0, 0, 0.14), 0 1px 18px 0 rgba(0, 0, 0, 0.12);
  overflow: hidden;
  padding: 14px 21px;
  text-align: center;
  font-size: 13px;
  font-weight: 100;
  -webkit-animation: fadein 0.5s, fadeout 0.5s 1.5s;
  animation: fadein 0.5s, fadeout 0.5s 1.5s;
}
.logoStyle {
  width: 54px;
}
.submitStyle2 {
  width: 60px;
  font-size: 12px;
  border: none;
  box-shadow: 0 2px 7px 0 rgb(214, 214, 214);
  background-color: #2585ff;
}
.editStyle {
  width: 60px;
  background: #fff !important;
  border-color: #2485ff !important;
  color: #2485ff !important;
  font-size: 12px;
}
::v-deep .modal-header {
  padding: 16px 16px 0;
}
::v-deep .modal-title {
  font-size: 16px;
  border-bottom: 2px solid #2585ff;
}
::v-deep .modal-content {
  border: none;
  border-radius: 8px;
}
::v-deep .modal-body {
  padding: 25px;
}
#snackbar.show {
  visibility: visible;
  -webkit-animation: fadein 0.5s, fadeout 0.5s 1.5s;
  animation: fadein 0.5s, fadeout 0.5s 1.5s;
}
.superCardMainDiv {
  .cardStyle {
    border-radius: 4px;
    margin: 8px 3px;
    padding: 12px 0;
    box-shadow: 0 2px 5px 0 rgba(0, 0, 0, 0.13);
  }
  .cardInfo {
    margin-bottom: 5px;
    display: flex;
    font-size: 12px;
  }
  .cardInfoAndCopy {
    // margin-bottom: 10px;
    display: flex;
    font-size: 12px;
    align-items: center;
    justify-content: space-between;
  }
  .cursorPointer {
    cursor: pointer;
  }
  .networkStyle {
    justify-content: center;
    align-items: center;
    display: flex;
    border-radius: 3px;
    background-color: #ffa090;
    margin: 4px 0 0 0;
    div {
      font-size: 10px;
      padding: 2px 4px 0 4px;
    }
  }

  ///////////// minimal Card //////////
  .card-card-number {
    margin-top: 0.4rem;
    background: #fff;
    box-shadow: 0 0 20px rgba(128, 163, 182, 0.18);
    border-radius: 6px;
    align-items: center;
    display: flex;
    flex: 1 1 100%;
    letter-spacing: normal;
    min-height: 48px;
    outline: none;
    /* padding: 0 16px; */
    position: relative;
    text-decoration: none;
  }
  .card-card-number .bank-logo {
    padding: 16px 0;
  }
  .card-card-number span {
    flex-grow: 1;
  }
  .card-card-number .bank-logo img {
    height: 35px;
    width: 35px;
  }
  .card-card-number .bank-name {
    text-align: center;
    font-size: 12px;
  }
  .card-card-number .card-number {
    text-align: left;
  }
  .card-input-element {
    display: none;
  }
  .card-input-element:checked + .card-card-number {
    background: #e7fde0;
    border-right: 2px solid #8bc34a !important;
    transition: 3ms;
  }
  .minimal {
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 0 7px;
  }
  .justify-content-evenly {
    justify-content: space-evenly !important;
  }
}
@media only screen and (max-width: 576px) {
  .logoStyle {
    width: 40px !important;
  }
  .superCardMainDiv .cardInfo {
    white-space: nowrap;
    font-size: 11px;
  }
  .networkStyle {
    div {
      padding: 2px 3px 0 3px !important;
    }
  }
}
</style>
