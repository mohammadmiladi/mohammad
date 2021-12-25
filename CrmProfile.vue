<template>
  <b-row class="p-bg-white">
    <b-col cols="12" class="px-1 pt-2">
      <div class="main-container">
        <div class="p-2 firstRow d-flex justify-content-between">
          <div class="userDetailCol d-flex justify-content-start align-items-center">
            <img class="img-fluid" v-if="userProfileData.image !== '' && userProfileData.image !== null" :src="userProfileData.image" alt="avatar" width="35" height="35" />
            <img class="img-fluid" v-else src="../../assets/images/users/default.png" alt="avatar" width="35" height="35" />
            <div class="Pinfo">
              <span class="text-start">{{ userProfileData.firstname }}{{ userProfileData.lastname }}</span>
              <div class="callDetail mt-1"><span class="p-callCounter">123</span>تماس <span class="p-timeEstimate">2:30</span> مدت پاسخگویی</div>
            </div>
          </div>
          <div class="p-selectButton d-flex flex-column justify-content-between">
            <!-- select form bayad ba dropdowni ke hosein neveshte ja be ja beshe  -->
            <b-form-select v-model="selectedOptions.selected" :options="selectedOptions.options"> </b-form-select>
            <div class="text-muted d-flex justify-content-around">
              <i class="fas fa-star font-size-10 text-warning"></i>
              <i class="fas fa-star font-size-10 text-warning"></i>
              <i class="fas fa-star font-size-10 text-warning"></i>
              <i class="fas fa-star font-size-10 text-warning"></i>
              <i class="fas fa-star font-size-10"></i>
            </div>
          </div>
        </div>
        <div class="chat-leftsidebar-nav mt-2">
          <b-tabs pills fill content-class="" class="parentTabsPadding">
            <b-tab active>
              <template v-slot:title>
                <i class="far fa-comment-alt font-size-20 d-sm-none"></i>
                <span class="d-none d-sm-block">تماس های جاری</span>
              </template>
              <b-card-text>
                <div>
                  <div :key="updateCall" style="max-height: 410px" id="chat-list">
                    <ul class="chat-list">
                      <li class="" v-for="(item, index) of onCallData" :key="index + 'call'">
                        <div :class="item.selectedCall ? 'activeCall' : 'each-call'" @click="goToUser(item)">
                          <div class="media">
                            <div class="align-self-center me-3" v-if="item.caller.image">
                              <img :src="`${item.caller.image}`" class="rounded-circle avatar-xs" alt />
                            </div>
                            <div class="avatar-xs align-self-center me-3" v-else>
                              <img src="../../assets/images/users/default.png" alt="image" />
                              <!-- <span class="avatar-title rounded-circle bg-soft bg-primary text-primary">{{ item.name.charAt(0) }}</span> -->
                            </div>
                            <div class="media-body overflow-hidden">
                              <h5 class="text-truncate font-size-14 mb-1">
                                {{ item.caller.fullName }}
                              </h5>
                              <p class="text-truncate mb-0">
                                {{ item.caller.role }}
                              </p>
                            </div>
                            <div v-if="item.status == 1">
                              <i class="fa-solid fa-phone-volume font-size-17 ms-1" style="color: #34c38f"></i>
                            </div>
                          </div>
                        </div>
                      </li>
                    </ul>
                  </div>
                </div>
              </b-card-text>
            </b-tab>
            <b-tab>
              <template v-slot:title>
                <i class="fas fa-users font-size-20 d-sm-none"></i>
                <span class="d-none d-sm-block">تاریخچه تماس</span>
              </template>
              <b-card-text>
                <div class="position-relative">
                  <input v-model="historyInput" @keyup="searchCall" class="searchInput" type="text" placeholder="جستجو..." />
                  <i class="fas fa-search search-icon"></i>
                </div>
                <ul @scroll="onScroll" class="chat-list" ref="scrollCombo">
                  <li v-for="item in callHistoryData" :key="item.id">
                    <div class="dateDiv">
                      {{ FormatedDate(item.date) }}
                    </div>
                    <div v-for="call in item.callLogs" :key="call.id">
                      <div :class="call.selectedCall ? 'activeCall' : 'each-call'" @click="goToUser(call)">
                        <div>
                          <div class="media">
                            <div class="align-self-center me-3" v-if="call.caller.image">
                              <img :src="call.caller.image" class="rounded-circle avatar-xs" alt />
                            </div>
                            <div class="avatar-xs align-self-center me-3" v-else>
                              <img src="../../assets/images/users/default.png" alt="image" />
                            </div>
                            <div class="media-body overflow-hidden">
                              <h5 class="text-truncate font-size-14 mb-1">
                                {{ call.caller.fullName }}
                              </h5>
                              <p class="text-truncate mb-0">
                                {{ call.caller.role }}
                              </p>
                            </div>
                            <div class="me-5 historyDuration">
                              {{ convertDate(call.createdDateTime) }}
                            </div>
                            <div>
                              <i v-if="call.state > 2" class="fa-solid fa-phone-missed ms-1 font-size-17" style="color: #f46a6a"></i>
                              <i
                                v-else
                                :class="call.isInComing ? 'fa-solid fa-phone-arrow-down-left' : 'fa-solid fa-phone-arrow-up-right'"
                                class="ms-1 font-size-17"
                                style="color: #34c38f ' : ' #6a7187"
                              ></i>
                            </div>
                          </div>
                        </div>
                      </div>
                    </div>
                  </li>
                </ul>
              </b-card-text>
            </b-tab>
            <b-tab>
              <template v-slot:title>
                <i class="far fa-address-book font-size-20 d-sm-none"></i>
                <span class="d-none d-sm-block">دفترچه تلفن</span>
              </template>
              <b-card-text>
                <div style="height: 410px">
                  <div class="position-relative">
                    <input v-model="contractInput" @keyup="searchContracts" class="searchInput" type="text" placeholder="جستجو..." />
                    <i class="fa fa-search search-icon"></i>
                  </div>
                  <ul @scroll="onScrollContract" class="chat-list" ref="scrollComboContracts">
                    <li v-for="data in contractsData" :key="data.key">
                      <div class="avatar-xs mb-3">
                        <span class="avatar-title rounded-circle bg-soft bg-primary text-primary charactarSpan">{{ data.key }}</span>
                      </div>
                      <div v-for="call in data.data" :key="call.id">
                        <div :class="call.selectedCall ? 'activeCall' : 'each-call'" @click="goToContractUser(call)">
                          <div>
                            <div class="contracts">
                              <div class="align-self-center me-3" v-if="call.image">
                                <img :src="call.image" class="rounded-circle avatar-xs" alt />
                              </div>
                              <div class="avatar-xs align-self-center me-3" v-else>
                                <img src="../../assets/images/users/default.png" alt="image" />
                              </div>
                              <div class="media-body overflow-hidden">
                                <h5 class="text-truncate font-size-14 mb-1">
                                  {{ call.fullName }}
                                </h5>
                              </div>
                            </div>
                          </div>
                        </div>
                      </div>
                    </li>
                  </ul>
                </div>
              </b-card-text>
            </b-tab>
          </b-tabs>
        </div>
      </div>
    </b-col>
  </b-row>
</template>

<script>
var signalR = require('@microsoft/signalr')
import crmDataProvider from '../../services/crm'
import JWT from '../../services/jwt'
import { API_URL } from '../../../config/HTTP'
import { userProfileMixins } from '../../utils/mixins'

export default {
  name: 'user-profile',
  mixins: [userProfileMixins],
  components: {},
  data() {
    return {
      updateCall: 1,
      selectedOptions: {
        selected: 'a',
        options: [
          { value: 'a', text: 'فعال' },
          { value: 'b', text: 'غیرفعال' },
          { value: 'c', text: 'در حال بررسی' },
          { value: 'd', text: 'مسدود شده' },
        ],
      },
      onCallData: [],
      callHistoryData: [],
      contractsData: [],
      loadMoreDataFlag: true,
      loadMoreData: true,
      connection: '',
      historyInput: '',
      contractInput: '',
      historyPagination: {
        pageSize: 3,
        pageNumber: 1,
        search: '',
      },
      contractsPagination: {
        pageSize: 3,
        pageNumber: 1,
        search: '',
      },
      form: {
        message: '',
      },
    }
  },
  methods: {
    getLastState(type) {
      crmDataProvider.getLastState().then((res) => {
        let myData = res.data.data
        myData.map((item) => {
          if (item.status == 1) {
            item.selectedCall = true
          } else {
            item.selectedCall = false
          }
        })
        if (type == 1) {
          this.onCallData = myData
        } else if (type == 2) {
          return myData
        }
      })
    },
    getHistoryData(pagination, type) {
      if (this.loadMoreDataFlag) {
        crmDataProvider.getHistory(pagination).then((res) => {
          let myData = res.data.data
          myData.map((item) => {
            item.callLogs.map((call) => {
              call.selectedCall = false
            })
          })
          if (type === 1) {
            this.callHistoryData = myData
          } else if (type === 2) {
            if (res.data.data.length !== 0) {
              this.callHistoryData.push(...myData)
            } else {
              this.loadMoreDataFlag = false
            }
          } else if (type === 3) {
            this.callHistoryData = []
            setTimeout(() => {
              this.callHistoryData = myData
            }, 500)
          }
        })
      }
    },
    getContractsData(pagination, type) {
      if (this.loadMoreData) {
        crmDataProvider.getContracts(pagination).then((res) => {
          let myData = res.data.data

          myData.map((item) => {
            item.data.map((call) => {
              call.selectedCall = false
            })
          })
          if (type === 1) {
            this.contractsData = myData
          } else if (type === 2) {
            if (res.data.data.length !== 0) {
              this.contractsData.push(...myData)
            } else {
              this.loadMoreData = false
            }
          } else if (type === 3) {
            this.contractsData = []
            setTimeout(() => {
              this.contractsData = myData
            }, 500)
          }
        })
      }
    },
    onScroll({ target: { scrollTop, clientHeight, scrollHeight } }) {
      if (scrollTop + clientHeight >= scrollHeight) {
        this.historyPagination.pageNumber++
        this.getHistoryData(this.historyPagination, 2)
      } else return
    },
    onScrollContract({ target: { scrollTop, clientHeight, scrollHeight } }) {
      if (scrollTop + clientHeight >= scrollHeight) {
        this.contractsPagination.pageNumber++
        this.getContractsData(this.contractsPagination, 2)
      } else return
    },
    searchCall() {
      this.loadMoreDataFlag = true
      this.historyPagination.search = this.historyInput
      this.historyPagination.pageNumber = 1
      this.$refs.scrollCombo.scrollTop = 0
      this.$nextTick(function () {
        this.getHistoryData(this.historyPagination, 3)
      })
    },
    searchContracts() {
      this.loadMoreData = true
      this.contractsPagination.search = this.contractInput
      this.contractsPagination.pageNumber = 1
      this.$refs.scrollComboContracts.scrollTop = 0
      this.$nextTick(function () {
        this.getContractsData(this.contractsPagination, 3)
      })
    },
    goToUser(selectCall) {
      this.callHistoryData.map((item) => {
        item.callLogs.map((call) => {
          call.selectedCall = false
        })
      })
      this.callHistoryData.map((item) => {
        item.callLogs.map((call) => {
          if (call.id == selectCall.id) {
            call.selectedCall = true
          }
        })
      })
      // this.$router.push(`/crm/user-information/${selectCall.caller.id}?navLink=UserGalance`).catch(() => {})
      this.$router.push(`/crm/user-information/21a58be9-1a37-473b-9789-497af6e02bb9?navLink=UserGalance`).catch(() => {})
    },
    goToContractUser(selectCall) {
      this.contractsData.map((item) => {
        item.data.map((call) => {
          call.selectedCall = false
        })
      })
      this.contractsData.map((item) => {
        item.data.map((call) => {
          if (call.id == selectCall.id) {
            call.selectedCall = true
          }
        })
      })
      // this.$router.push(`/crm/user-information/${selectCall.id}?navLink=UserGalance`).catch(() => {})
      this.$router.push(`/crm/user-information/21a58be9-1a37-473b-9789-497af6e02bb9?navLink=UserGalance`).catch(() => {})
    },
    durationTime(start, end) {
      return this.convertTime(start)
    },
    compareArrays(left, right, compareFunction) {
      return left.filter((leftValue) => !right.some((rightValue) => compareFunction(leftValue, rightValue)))
    },
  },
  created() {
    //////// start Socket ///////
    this.connection = new signalR.HubConnectionBuilder()
      .withUrl(`${API_URL}/CallManager/CallManagerHub`, { accessTokenFactory: () => JWT.getToken() })
      .withAutomaticReconnect()
      .configureLogging(signalR.LogLevel.Information)
      .build()
    // this.connection
    //   .start()
    //   .then(() => this.connection.invoke('login', JWT.getToken()))
    //   .catch(function(err) {
    //     return console.error('err', err)
    //   })
    this.connection.onclose(function (error) {
      if (error) {
        console.log(`Connection closed with error: ${error}`)
        // const last = this.getLastState(2)
        // const isSameUser = (a, b) => a.value == b.value && a.display == b.display
        // const first = this.compareArrays(this.onCallData, last, isSameUser)
        // const second = this.compareArrays(last, this.onCallData, isSameUser)
        // const result = [...first, ...second]
        // this.onCallData.push(...result)
        this.connection.start()
      } else {
        // const last = this.getLastState(2)
        // const isSameUser = (a, b) => a.value == b.value && a.display == b.display
        // const first = this.compareArrays(this.onCallData, last, isSameUser)
        // const second = this.compareArrays(last, this.onCallData, isSameUser)
        // const result = [...first, ...second]
        // this.onCallData.push(...result)
        this.connection.start()
      }
    })
    /////// End Socket ////////////
  },
  beforeMount() {
    this.getHistoryData(this.historyPagination, 1)
    this.getContractsData(this.contractsPagination, 1)
    this.getLastState(1)
  },
  mounted() {
    var thisVue = this
    thisVue.connection.start()
    thisVue.connection.on('broadcastMessage', (response) => {
      console.log('res signalR', JSON.parse(response))
      var removeByAttr = function (arr, attr, value) {
        var i = arr.length
        while (i--) {
          if (arr[i] && arr[i].hasOwnProperty(attr) && arguments.length > 2 && arr[i][attr] === value) {
            arr.splice(i, 1)
          }
        }
        return arr
      }
      if (JSON.parse(response).status == 0) {
        this.onCallData.push(JSON.parse(response))
      }
      if (JSON.parse(response).status == 1) {
        this.onCallData = removeByAttr(this.onCallData, 'uniqueId', JSON.parse(response).uniqueId)
        JSON.parse(response).selectedCall = true
        setTimeout(() => {
          this.onCallData.unshift(JSON.parse(response))
        }, 500)
      }
      if (JSON.parse(response).status >= 2) {
        this.onCallData = removeByAttr(this.onCallData, 'uniqueId', JSON.parse(response).uniqueId)
      }
      this.updateCall++
    })
    thisVue.connection.serverTimeoutInMilliseconds = 100000
  },
  beforeDestroy() {
    this.connection.off('broadcastMessage')
    this.connection.stop()
  },
}
</script>
<style scoped>
.historyDuration {
  color: #495057 !important;
  font-size: 13px !important;
  font-weight: 400;
}
.p-bg-white {
  background-color: #ffffff !important;
}
.p-callCounter,
.p-timeEstimate {
  margin: auto 2px !important;
  direction: rtl !important;
}
.callDetail {
  font-size: 13px;
  font-weight: 400;
  color: #74788d;
}
.userDetailCol img {
  border-radius: 100%;
  margin-left: 5px;
}
.Pinfo > span {
  color: #495057;
  font-size: 15px;
}
.p-selectButton select:focus,
.p-selectButton select,
.p-selectButton select:focus-visible {
  border: none !important;
  outline: none !important;
}
.p-selectButton select {
  background-color: #e0e5fa;
  color: #556ee6;
  border-radius: 15px;
}
.search-icon {
  width: 15px;
  font-size: 14px;
  position: absolute;
  right: 13px;
  top: 12px;
  line-height: 38px;
  color: #495057 !important;
}
.searchInput {
  margin: 10px 0;
  width: 100%;
  padding: 10px 30px;
  border: 1px solid #ececec;
  height: 40px;
  background: #ffffff;
  border-radius: 50px;
  outline: none;
}
input:focus::placeholder {
  color: transparent;
}
::v-deep ol,
ul {
  padding-left: 0 !important;
}
.chat-list {
  height: calc(100vh - 380px);
  overflow-y: scroll;
  margin: 0;
}
.chat-list li.active a {
  background-color: #fff;
  border-color: transparent;
  box-shadow: 0px 1px 12px rgba(18, 38, 63, 0.22);
}
.each-call {
  display: block;
  padding: 14px 16px;
  color: #74788d;
  -webkit-transition: all 0.4s;
  transition: all 0.4s;
  border-top: 1px solid #eff2f7;
  border-radius: 4px;
}
.each-call:hover {
  cursor: pointer;
  background-color: #fff;
  border-color: transparent;
  box-shadow: 0px 1px 12px rgba(18, 38, 63, 0.22);
}
::v-deep .chat-leftsidebar-nav .tabs .nav-item a {
  padding: 6px !important;
}
::v-deep .chat-leftsidebar-nav ul.nav-pills {
  border: 1px solid #f4f4f4;
}
.contracts {
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  -webkit-box-align: start;
  -ms-flex-align: start;
  align-items: center;
}
.each-call {
  padding: 0px !important;
}
.chat-list li :nth-child(2) a {
  border-top: none !important;
}
.each-call > div {
  padding: 14px 16px !important;
}
.activeCall {
  display: block;
  padding: 14px 16px;
  color: #74788d;
  background-color: #fff;
  border-color: transparent;
  box-shadow: 0px 1px 12px rgba(18, 38, 63, 0.22);
}
.dateDiv {
  padding: 3px 10px;
  background: #f8f9fa;
  border-radius: 15px;
  margin: 10px;
  color: #74788d;
  font-size: 14px;
}
.main-container {
  height: calc(100vh - 138px);
}
::v-deep .parentTabsPadding > div:first-of-type {
  padding: 0.5rem;
}
::-webkit-scrollbar {
  display: none;
}
.charactarSpan {
  margin: 10px 8px !important;
}
.chat-list li .kharkhasak:nth-child(2) a {
  border: none !important;
}
</style>
