<template>
  <div class="wrapper">
    <div class="container-fluid">
      <div class="row">

        <div class="col-sm-12">
          <div class="page-title-box">
              <h4 class="page-title">Invest</h4>
          </div>
        </div>

      </div>
      <div class="card-box">

        <!--<div class="tab-content px-3" v-if="me_wallet.balances[0].balance<0.1">-->
        <!--<div class="form-group row justify-content-md-center mt-4">-->
        <!--<div class="col-xl-6 col-lg-6">-->
        <!--<div class="alert alert-warning text-center">-->
        <!--You must have at least 0.1 ETH in your wallet to perform the verification.<br>-->
        <!--<router-link :to="{name:'wallet'}"><b>Deposit Now</b></router-link>-->
        <!--</div>-->
        <!--</div>-->
        <!--</div>-->
        <!--</div>-->
        <div class="tab-content px-3" v-if="verification_level<3">
          <div class="row justify-content-md-center">
            <div class="col-md-6">
              <div class="text-center">
                <div class="stepwizard">
                  <div class="stepwizard-row">
                    <div class="stepwizard-step">
                      <button type="button" class="btn btn-circle"
                              :class="{'btn-secondary':step>1,'btn-primary':step===1}">1
                      </button>
                    </div>
                    <div class="stepwizard-step">
                      <button type="button" class="btn btn-circle"
                              :class="{'btn-default':step<2, 'btn-secondary':step>2, 'btn-primary':step===2}">2
                      </button>
                    </div>
                    <div class="stepwizard-step">
                      <button type="button" class="btn btn-default btn-circle"
                              :class="{'btn-default':step<3, 'btn-secondary':step>3, 'btn-primary':step===3}">3
                      </button>
                    </div>
                    <div class="stepwizard-step">
                      <button type="button" class="btn btn-default btn-circle"
                              :class="{'btn-default':step<4, 'btn-secondary':step>4, 'btn-primary':step===4}">4
                      </button>
                    </div>
                  </div>

                </div>
                <p class="logo-lg m-0">
                  <span v-if="step===1">Deposit</span>
                  <span v-else-if="step===2">Verify Deposit</span>
                  <span v-else-if="step===3">Invest</span>
                    <span v-else-if="step===4">KYC Application Submitted</span>
                </p>
              </div>
            </div>
          </div>
          <div v-show="step===1">
            <div class="card-box">
                <div class="tab-pane fade show active text-center" id="deposit">
                  <!--<div class="alert alert-warning">-->
                  <!--You can send <b>ETH</b> and <b>ETH tokens</b> to this address, but DO NOT send other tokens (ex: Ethereum Classic (ETC), Bitcoin (BTC), Ripple (XRP))!-->
                  <!--</div>-->
                  <!--<hr>-->
                  <h4>Your ETH Wallet Address</h4>
                  <p style="word-wrap: break-word;" class="mt-3">{{me_wallet.eth_address}}</p>
                  <hr>
                  <h4>Your ETH Wallet in QR Code</h4>
                  <qrcode :value="me_wallet.eth_address" :options="{ size: 150 }"/>
                  <hr>
                  <!--<a-->
                  <!--:href="'https://buy.coinbase.com?code=95f65a41-4643-58a4-8eb2-fda57ada4392&address=' + me_wallet.eth_address +'&prefill_email='+me.user.email+ '&redirect_uri=https://app.gullin.io/wallet'"-->
                  <!--target="_blank">-->
                  <!--Buy with Coinbase-->
                  <!--</a>-->
                  <!--<hr>-->
                  <div class="text-center">
                    <a :href="'https://etherscan.io/address/' + me_wallet.eth_address" target="_blank" class="btn btn-primary text-white">See On Etherscan</a>
                  </div>
                    <div class="form-group row justify-content-md-center mt-4">
                  <div class="col-xl-2 col-lg-3">
                    <button class="btn btn-primary btn-block" @click="get_balance()">Next Step</button>
                  </div>
                </div>
                </div>

            </div>

          </div>
          <div v-show="step===2" v-if='this.eth_balance==0'>
              <div class="form-group row justify-content-md-center mt-4">
            <div class="col-xl-2 col-lg-3">
              <button class="btn btn-primary btn-block" @click="update_address()">Next Step</button>
            </div>
          </div>
          </div>

          <div v-show="step===3">


              <div class="modal-dialog modal-lg">
                <div class="modal-content">
                  <div class="modal-header">
                    <h4 class="modal-title">Participate</h4>
                  </div>
                  <div class="modal-body row" v-if="!show_invest_summary">
                    <div class="col-md-6 border-right">
                      <div class="row form-group">
                        <div class="col-10">
                          <label class="control-label">Amount</label>
                          <a href="#" class="pull-right" @click="maxInvestAmount()">MAX</a>
                          <input v-model="amount" type="text" class="form-control" placeholder="0.00" :disabled="isRestricted()">
                        </div>
                        <label class="col-2 col-form-label mt-4">ETH</label>
                      </div>
                      <div class="row form-group">
                        <div class="col-md-12">
                          <label class="control-label">Token Sale Destination</label>
                          <input type="text" class="form-control" :value="current_token_detail.crowd_sale_contract_address" disabled>
                        </div>
                      </div>
                      <div class="row form-group">
                        <div class="col-md-12">
                          <label class="control-label">My Wallet Address</label>
                          <input type="text" class="form-control" :value="me_wallet.eth_address" disabled>
                        </div>
                      </div>
                      <div class="row form-group">
                        <div class="col-md-12">
                          <label class="control-label">My Private Key</label>
                          <a href="https://gullin.zendesk.com/hc/en-us/articles/360001358553-Where-is-my-private-key-" target="_blank" class="pull-right">Where is it?</a>
                          <input v-model="private_key" type="text" class="form-control" placeholder="Private Key" :disabled="isRestricted()">
                        </div>
                      </div>
                      <div class="text-center">
                        <button v-if="!show_invest_summary" type="button" class="btn" @click="investPreCheck()"
                                :class="{'btn-secondary':isRestricted(), 'btn-primary':!isRestricted()}">Participate
                        </button>
                      </div>
                    </div>
                    <div class="col-md-6">
                      <div class="alert alert-primary" v-if="!isRestricted()">
                        You are participating in <b>{{current_company.name}}</b>, please make sure you are on the right page!
                      </div>
                      <div class="alert alert-danger" v-else-if="isRestricted()===4">
                        This Token Sale is ended.<br>
                      </div>
                      <div class="alert alert-danger" v-else-if="isRestricted()===1">
                        You must be verified through KYC in settings before you are able to participate in this Token Sale.<br>
                        <b>
                          <router-link router-link :to="{name:'settings_verification'}" data-dismiss="modal">Verify Now</router-link>
                        </b>
                      </div>
                      <div class="alert alert-danger" v-else-if="isRestricted()===2">
                        We are sorry, your region is restricted by this Token Sale.
                      </div>
                      <div class="alert alert-danger" v-else-if="isRestricted()===3">
                        You don't have enough ETH balance in your wallet to participate in this Token Sale.<br>
                        <b>
                          <router-link router-link :to="{name:'wallet'}" data-dismiss="modal">Deposit Now</router-link>
                        </b>
                      </div>
                      <table class="table">
                        <tbody>
                        <tr>
                          <td>My Balance</td>
                          <td><b>{{eth_balance}}</b> ETH</td>
                        </tr>
                        <tr>
                          <td>Price</td>
                          <td> {{current_token_detail.price}} {{current_token_detail.price_unit}} / {{current_token_detail.token_code}}</td>
                        </tr>
                        <tr>
                          <td>Threshold</td>
                          <td v-if="current_token_detail.threshold"> {{current_token_detail.threshold}} ETH</td>
                          <td v-else> None</td>
                        </tr>
                        <tr>
                          <td>Restrictions</td>
                          <td v-html="current_token_detail.restrictions" v-if="current_token_detail.restrictions"></td>
                          <td v-else> None</td>
                        </tr>
                        <tr>
                          <td> Bonus</td>
                          <td v-html="current_token_detail.bonus" v-if="current_token_detail.bonus"></td>
                          <td v-else> None</td>
                        </tr>
                        </tbody>
                      </table>
                    </div>
                  </div>
                  <div class="modal-body row justify-content-center" v-else>
                    <div class="col-md-10">
                      <h4 class="text-center md-2"><b>Confirm Transaction</b></h4>
                      <div class="alert alert-primary text-center">
                        Please make sure everything is correct!
                      </div>
                      <table class="table">
                        <tbody>
                        <tr>
                          <td>Token Sale Name</td>
                          <td><b>{{current_company.name}}</b></td>
                        </tr>
                        <tr>
                          <td>Amount Sending</td>
                          <td><b>{{amount}} ETH</b></td>
                        </tr>
                        <tr>
                          <td>Estimated Transaction Fee</td>
                          <td><b>0.00021 ETH</b></td>
                        </tr>
                        <tr>
                          <td>Total amount</td>
                          <td><b>{{(Number(amount) + Number('0.00021')).toFixed(7)}} ETH</b></td>
                        </tr>
                        </tbody>
                      </table>
                      <p class="text-center" v-show="tx_loading">
                        <spinner></spinner>
                        Sending transaction, it may take 1-5 minutes to get the confirmation message. (Closing browser will not interrupt the transaction)
                      </p>
                      <p class="text-center text-success" v-show="transaction_success">Transaction Success!</p>
                      <p class="text-center text-danger" v-show="transaction_failed">{{error_message}}</p>
                    </div>
                  </div>
                  <div class="modal-footer">
                    <button v-if="show_invest_summary&&!transaction_success" type="button" class="btn btn-secondary" @click="show_invest_summary=false">Back</button>
                    <button v-if="show_invest_summary&&!transaction_success" type="button" class="btn" :class="{'btn-secondary':tx_loading, 'btn-primary':!tx_loading}" @click="sendEth()">
                      <span v-show="tx_loading">Sending Transaction</span>
                      <span v-show="!tx_loading">Confirm</span>
                    </button>

                    <router-link v-if="transaction_success" class="btn btn-primary" :to="{name:'wallet'}" data-dismiss="modal">
                      View Transaction
                    </router-link>
                  </div>
                </div><!-- /.modal-content -->
              </div><!-- /.modal-dialog -->

              <div class="form-group row justify-content-md-center mt-4">

            <div class="col-xl-2 col-lg-3">
              <button class="btn btn-primary btn-block" @click="update_address()">Next Step</button>
            </div>
          </div>

          </div>

          <div v-show="step===4">

          </div>


        </div>

        <div class="tab-content px-3 text-center" v-else-if="verification_level===3 ||step===4">
          <div class="form-group row justify-content-md-center mt-4">
            <div class=" col-lg-6">
              <div class="alert alert-success">
                You have successfully submitted your identity verification application, please wait us up to 24 hours to process it.
              </div>
            </div>
          </div>
        </div>

        <div class="tab-content px-3" v-else-if="verification_level===4">
          <div class="form-group row justify-content-md-center mt-4">
            <div class="col-xl-4 col-lg-6">
              <div class="alert alert-success text-center">
                Your identity has been verified.
              </div>
              <div class="text-center">
                <a href="#" class="btn btn-primary" data-toggle="modal" data-target="#aiv_modal" v-if="me.verification_level===4&&me.nationality==='United States'">Apply for Accredited Investor</a>
              </div>
            </div>
          </div>
        </div>

        <div class="tab-content px-3" v-else-if="verification_level===5">
          <div class="form-group row justify-content-md-center mt-4">
            <div class="col-xl-4 col-lg-6">
              <div class="alert alert-success text-center">
                We have received your Accredited Investor verification request, you will shortly receive an email about how to proceed.
              </div>
            </div>
          </div>
        </div>

        <div class="tab-content px-3" v-else-if="verification_level===6">
          <div class="form-group row justify-content-md-center mt-4">
            <div class="col-xl-4 col-lg-6">
              <div class="alert alert-success text-center">
                Your identity has been verified.
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>


<script>
  import { mapGetters } from 'vuex'
  import Spinner from '../../components/Spinner'

  export default {
    name: 'KYCVerificationPlugin',
    components: {
      Spinner
    },
    data() {
      return {
        first_name: '',
        last_name: '',

        birthday_month: '',
        birthday_day: '',
        birthday_year: '',

        nationality: '',

        address1: '',
        address2: '',
        city: '',
        state: '',
        zipcode: '',
        country: '',

        id_type: '',
        id_front: '',
        id_front_file_name: '',
        id_back: '',
        id_back_file_name: '',
        id_holding: '',
        id_holding_file_name: '',

        step: 1,
        uploading: false,
        error_message: '',
        success_message: '',

        loading: true,

        amount: '',
        private_key: null,

        show_invest_summary: false,
        transaction_success: '',
        transaction_failed: '',
        tx_loading: false,

        error_message: '',
        loading: false,
        ether_ballance: 0,
      }
    },
    computed: {
      ...mapGetters({
        verification_level: 'verification_level',
        me_phone: 'me_phone',

        is_login: 'is_login',
        current_company: 'current_company',
        current_token_detail: 'current_token_detail',
        me_wallet: 'me_wallet',
        balances: 'balances',
        me: 'me',
      }),
    },
    methods: {
      get_balance() {
          this.step += 1

        if (this.eth_balance == 0){

          setInterval(function () {
              this.get_eth_balance();
          }.bind(this), 30000);

      } else {
        this.get_eth_balance();
      }
      },

      get_eth_balance() {
        this.$store.dispatch('syncEthBalance', this)
          .then(() => {

              if (response.result != 0)
              {
                loading = false,
                this.eth_balance = response.result
              }

          })
          .catch((error) => {
            this.error_message = error.data.error
          })
      },

      update_personal_detail() {
        if (!(this.birthday_year && this.birthday_month && this.birthday_day)) {
          this.error_message = 'Birthday is required'
          return
        }
        const form_data = {
          update: 'name_birthday',
          birthday: this.birthday_year + '-' + this.birthday_month + '-' + this.birthday_day,
          first_name: this.first_name,
          last_name: this.last_name,
          nationality: this.nationality,
        }
        this.$store.dispatch('updateMe', form_data)
          .then(() => {
            this.step += 1
            this.error_message = ''
          })
          .catch((error) => {
            this.error_message = error.data.error
          })
      },
      update_address() {
        this.step += 1
      },
      upload_id() {
        if (!this.id_type || !this.id_holding || !this.id_front) {
          this.error_message = 'All fields are required'
          return
        }
        this.error_message = ''
        this.uploading = true
        this.step += 1
        const form_data = {
          'official_id_type': this.id_type,
          'official_id_front_base64': this.id_front,
          'official_id_back_base64': this.id_back,
          'user_holding_official_id_base64': this.id_holding,
          'investor_user': this.me.id
        }

        // this.$store.dispatch('uploadID', form_data)
        //   .then(() => {
        //     this.step += 1
        //     this.error_message = ''
        //     this.uploading = false
        //     //this.$store.dispatch('refresh')
        //   })
        //   .catch((error) => {
        //     this.error_message = error.data
        //     this.uploading = false
        //   })
      },

      onFileChange(type, e) {
        // get file list by event listener
        let files = e.target.files || e.dataTransfer.files
        // if no files then return
        if (!e.target.files && !e.dataTransfer.files) return
        // else file is the first element in file list
        const file = files[0]

        // if file size is larger than 4mb, return and show error message
        if (file.size >= 4000000) {
          this.error_message = 'The image size is larger than the 4MB.'
          return
        } else if (file.size <= 100000) {
          this.error_message = 'The image size is smaller than the 100KB.'
          return
        } else {
          this.error_message = ''
        }

        // init file reader for convert base64 data
        const reader = new FileReader()
        if (type === 'id_front') {
          // convert image to base64 and save to id_front field
          const self = this
          reader.addEventListener('load', function () {
            self.id_front = reader.result
          }, false)
          reader.readAsDataURL(file)
          // file name
          this.id_front_file_name = file.name
        }
        else if (type === 'id_back') {
          // convert image to base64 and save to id_back field
          const self = this
          reader.addEventListener('load', function () {
            self.id_back = reader.result
          }, false)
          reader.readAsDataURL(file)
          // file name
          this.id_back_file_name = file.name
        }
        else if (type === 'id_holding') {
          // convert image to base64 and save to id_holding field
          const self = this
          reader.addEventListener('load', function () {
            self.id_holding = reader.result
          }, false)
          reader.readAsDataURL(file)
          // file name
          this.id_holding_file_name = file.name
        }
      },
      removeFile(type) {
        if (type === 'id_front') this.id_front = null
        else if (type === 'id_back') this.id_back = null
        else if (type === 'id_holding') this.id_holding = null
      },

      accreditedInvestorVerification() {
        this.$store.dispatch('accreditedInvestorVerification')
          .then(() => {
            this.$store.dispatch('getMe')
            $('#aiv_modal').modal('hide')
          })
      },
      getCompany(company_name) {
        this.loading = true
        this.$store.dispatch('getCompany', company_name)
          .then(() => {
            this.loading = false
          })
          .catch(() => {
            this.$router.push({ name: '404' })
          })
      },
      timeCounter(start, end) {
        /* global moment:true */
        // Haven't start
        if (moment().diff(start, 'minutes') < 0) {
          let rest = -moment().diff(start, 'days') + ' days '
          if (rest === '0 days ') {
            rest = -moment().diff(start, 'hours') + ' hours '
          }
          if (rest === '0 hours ') {
            rest = -moment().diff(start, 'minutes') + ' minutes '
          }
          return 'Starts in ' + rest
        }
        // Started
        else if (moment().diff(end, 'minutes') < 0) {
          let rest = -moment().diff(end, 'days') + ' days '
          if (rest === '0 days ') {
            rest = -moment().diff(end, 'hours') + ' hours '
          }
          if (rest === '0 hours ') {
            rest = -moment().diff(end, 'minutes') + ' minutes '
          }
          return 'Ends in ' + rest
        }
        // Ended
        else {
          let rest = moment().diff(end, 'days') + ' days '
          if (rest === '0 days ') {
            rest = moment().diff(end, 'hours') + ' hours '
          }
          if (rest === '0 hours ') {
            rest = moment().diff(end, 'minutes') + ' minutes '
          }
          return 'Ended ' + rest + 'ago'
        }
      },
      timeFromNow(time) {
        return moment(time).fromNow()
      },
      isRestricted() {
        if (!this.is_login) return true
        // User if verified?
        if (this.verification_level < 4) return 1
        // Check date
        if (this.timeCounter(this.current_company.token_detail.start_datetime, this.current_company.token_detail.end_datetime).includes('Ended')) return 4
        // Check restricted country
        const restricted_country_list = JSON.parse(this.current_token_detail.restricted_country_list)
        if (restricted_country_list.indexOf(this.me.nationality) >= 0) return 2
        // Check balance
        const eth_balance = this.me_wallet.balances[0].balance
        if (eth_balance <= this.current_token_detail.threshold) return 3
      },
      investPreCheck() {
        // Check List before send ETH
        // Check restricted
        if (this.isRestricted()) return
        // Check threshold
        if (this.amount && this.amount < this.current_token_detail.threshold) return
        // Check private key
        if (!this.private_key) return

        this.show_invest_summary = true
      },

      getETHBalanceByAddress(){

      },

      sendEth() {
        this.error_message = ''
        if (this.tx_loading) return

        this.tx_loading = true

        const form_data = {
          to_address: this.current_token_detail.crowd_sale_contract_address,
          value: this.amount.toString(),
          private_key: this.private_key,
        }
        this.$store.dispatch('sendEth', form_data)
          .then(() => {
            this.transaction_success = true
            this.tx_loading = false
          })
          .catch((error) => {
            this.error_message = error.toString().split('\n')[0]
            this.transaction_failed = true
            this.tx_loading = false
          })
      },
      maxInvestAmount() {
        if (this.isRestricted()) return
        if (this.eth_balance < 0.000021) {
          this.transaction_failed = true
          this.error_message = 'Your Balance is lower than 0.000021, which is the Minimal transaction fee for ETH.'
        }
        else {
          this.amount = this.eth_balance - 0.000021
        }
      }
    },
    created() {
      this.first_name = this.me.first_name
      this.last_name = this.me.last_name
      this.nationality = this.me.nationality

      if (this.$route.params.company_name) {
        this.getCompany(this.$route.params.company_name)
      }

      if (this.me.birthday) {
        const b_array = this.me.birthday.split('-')
        this.birthday_year = b_array[0]
        this.birthday_month = b_array[1]
        this.birthday_day = b_array[2]
      }
      if (this.me.address[0]) {
        this.address1 = this.me.address[0].address1
        this.address2 = this.me.address[0].address2
        this.city = this.me.address[0].city
        this.state = this.me.address[0].state
        this.zipcode = this.me.address[0].zipcode
        this.country = this.me.address[0].country
      }
    },
  }
</script>

<style scoped>
  .tabs-bordered li a.router-link-exact-active {
    border-bottom: 2px solid #2B2E58 !important;
  }

  .stepwizard-step p {
    margin-top: 10px;
  }

  .stepwizard-row {
    display: table-row;
  }

  .stepwizard {
    display: table;
    width: 100%;
    position: relative;
  }

  .stepwizard-step button[disabled] {
    opacity: 1 !important;
    filter: alpha(opacity=100) !important;
  }

  .stepwizard-row:before {
    top: 14px;
    bottom: 0;
    position: absolute;
    content: " ";
    width: 100%;
    height: 1px;
    background-color: #ccc;
    z-order: 0;
  }

  .stepwizard-step {
    display: table-cell;
    text-align: center;
    position: relative;
  }

  .btn-circle {
    width: 30px;
    height: 30px;
    text-align: center;
    padding: 6px 0;
    font-size: 12px;
    line-height: 1.428571429;
    border-radius: 15px;
  }

  .dropzone-area {
    display: block;
    width: 100%;
    font-size: 1rem;
    line-height: 1.25;
    color: #464a4c;
    height: 100px;
    border: 1px dashed #464a4c;
    border-radius: 5px;
  }

  .dropzone-area input {
    position: absolute;
    cursor: pointer;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 100%;
    opacity: 0;
  }

  .dropzone-text {
    position: absolute;
    top: 50%;
    text-align: center;
    transform: translate(0, -50%);
    width: 100%;
  }

  .dropzone-text span {
    display: block;
    line-height: 1.9;
  }

  .canvas {
    width: 100%;
  }
</style>
