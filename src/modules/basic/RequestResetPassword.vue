<template>
  <div class="row holder" style="justify-content: center; align-items: center;">
    <div class="tag-line">
      <span style="width:100%;float:left;text-align:center;font-size:20px;margin-bottom:20px;">
        <b>Request to Reset</b>
      </span>
      <div class="input-holder">
        <div class="login-message-holder login-spacer" v-if="errorMessage != ''">
            <span class="text-danger text-center" v-if="successMessage === null && errorMessage !== null"><b>Oops!</b> {{errorMessage}}</span>
            <span class="text-primary text-center" v-else>{{successMessage}}</span>
        </div>
        <div class="login-spacer" style="margin-bottom: 25px !important;text-align: justify;" v-if="hide === true">
          We send recovery email to your email address at <b>{{email}}</b>.
          Please give us a moment, it may take few minutes. Please check your email address to continue.
        </div>
        <input type="text" name="username" placeholder="Type your Email Address" class="form-control form-control-login" v-model="email" v-if="hide === false">
        <br>
        <button class="btn btn-secondary reset-button login-spacer" v-on:click="request()" v-if="hide === false">Send Request</button>   
        <br><br><br><br>
        <div style="width: 100%;text-align: center;">
          <p class="account">Cancel?</p>
        </div>
        <hr> 
        <button class="btn btn-primary back-button login-spacer" @click="redirect('/login')">Back to login</button>
        <br>
        <br>
     </div>
    </div>
  </div>
</template>
<style scoped lang="scss">
@import "~assets/style/colors.scss";
.form-control-login {
  margin-top: 10px;
  height: 45px !important;
  border-radius: 30px;
  border: 1px solid rgb(219, 218, 218);
  border-width: .5px;
  margin-bottom: 5px;
}

.account {
  color: $primary;
}

.tag-line{
  width: 25%;
  background-color: rgb(255, 255, 255);
  border-radius: 15px;
  padding: 30px;
}

.reset-button {
  border-radius: 30px;
  float: right;
  width: 120px;
  margin-bottom: 20px;
}

.back-button {
  border-radius: 30px;
  float: right;
  width: 120px;
}

.holder{
  margin-top: 25px;
  height: 85vh;
}
.custom-holder{
  margin-top: 0px;
}

.signup-header{
  height: 100px;
  color: #006600;
  width: 100%;
  float: left;
  text-align: center;
}

.signup-header img{
  width: auto !important;
  height: 70px;
}

.signup-header img:hover{
  cursor: pointer;
}

.header-title{
  width: 90%;
  margin:  25px 5% 0 5%;
  font-size: 24px;
  font-weight: 700px;
}

.input-holder{
  width: 90%;
  margin:  0 5% 0 5%;
}

.btn{
  height: 40px !important;
}

.form-control{
  height: 45px !important;
}

.site-title{
  margin-top: 25px;
  width: 100%;
  float: left;
}
.site-title img{
  width: 50px;
  width: 50px;
  float: left;
  margin-right: 10px;
  margin-left: 5%;
}
.site-title .app-name{
  float: left;
}

.input-group label{
  width: 100%;
  float: left;
  line-height: 50px;
  text-align: center;
}

.input-group label b:hover{
  cursor: pointer;
}
/*-------------- Extra Small Screen for Mobile Phones --------------*/
@media (max-width: 991px){
  .tag-line{
    box-shadow: 0 0 0 0 #fff !important;
    margin-top: 50px !important;
  }
}
</style>

<script>
import ROUTER from 'src/router'
import AUTH from 'src/services/auth'
import CONFIG from 'src/config.js'
import COMMON from 'src/common.js'
export default {
  name: '',
  mounted(){
  },
  data(){
    return{
      email: null,
      flag: false,
      errorMessage: null,
      successMessage: null,
      hide: false,
      config: CONFIG,
      common: COMMON
    }
  },
  methods: {
    request(){
      this.validate()
      let parameter = {
        email: this.email
      }
      if(this.flag === true){
        $('#loading').css({display: 'block'})
        this.APIRequest('accounts/request_reset', parameter).then(response => {
          $('#loading').css({display: 'none'})
          this.hide = true
          this.errorMessage = null
        })
      }
    },
    validate(){
      if(this.email === null || this.email === ''){
        this.flag = false
        this.errorMessage = 'Please enter your Email Address'
      } else if(AUTH.validateEmail(this.email) === false){
        this.errorMessage = 'You have entered an invalid email address.'
      } else {
        this.flag = true
      }
    },
    redirect(parameter){
      ROUTER.push(parameter)
    }
  }
}
</script>
