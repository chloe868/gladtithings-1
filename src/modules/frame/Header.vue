<template>
  <div>
    <div class="system-header">
      <!-- <a class="navbar-brand" v-on:click="redirect('dashboard')"> -->
        <img @click="redirect('dashboard')" :src="require('src/assets/img/white_logo.png')" class="logo-brand">
        <!-- <label class="navbar-brand hide-on-mobile text-white" v-html="common.APP_NAME_HTML"></label> -->
      <!-- </a> -->
    </div>
    <nav class="header-navbar">
      <span class="navbar-menu-toggler-md" v-bind:class="{'active-menu': menuFlag === true}" data-toggle="collapse" data-target="#idfactory" aria-controls="idfactory" aria-expanded="false" aria-label="Toggle navigation" v-on:click="makeActive('menu')">
          <i class="fa fa-bars" aria-hidden="true"></i>
      </span>
      <!-- <span class="left-menu-icons">
        <label class="account-type  hide-on-mobile bg-warning" v-if="!common.header.indexOf('status') && user !== null">{{user.type}}</label>
      </span> -->
      <span class="right-menu-icons">
        <div class="dropdown"> 
          <span style="padding-right: 200px; " class="nav-item" v-bind:class="{'active-menu': settingFlag === true}" data-toggle="dropdown" id="settings" aria-haspopup="true" aria-expanded="false" v-on:click="makeActive('dropdown')" v-bind:onkeypress="makeActive('')">
            <div v-if="user.profile !== null" style="width: 180px; height: 40px; margin-left: 20px;" class="row">
              <div class="column">
                 <img v-bind:src="config.BACKEND_URL + user.profile.url" style="width: 30px; height: 30px; border-radius: 100px; margin-top: 8px;">
              </div>
              <div class="column">
                <p style="font-size: 17px; margin-top: 12px;" v-if="user.profile !== null">&nbsp;&nbsp;{{user.username}}</p>
              </div>
            </div>
            <i class="fa fa-user-circle-o profile-icon" style="margin-top: 15px; font-size: 1.5em; margin-left: 10px;" v-else><span style="font-family: Poppins; font-size: 20px;">&nbsp;&nbsp;{{user.username}}</span></i>
            <span class="dropdown-menu dropdown-menu-right" aria-labelledby="settings">
              <span class="dropdown-item-profile">
                <span class="account-picture text-center">
                  <span class="profile-photo-header">
                    <span class="profile-image-holder-header"  v-if="user.profile !== null">
                      <img v-bind:src="config.BACKEND_URL + user.profile.url" >
                    </span>
                    <i class="fa fa-user-circle-o" v-else></i>
                  </span>
                </span>
                <span class="account-info text-center">{{user.username}}</span>
              </span>
              <span class="dropdown-item dropdown-item-menu-title">
                <label>Personal</label>
              </span>
              <span class="dropdown-item" v-on:click="redirect(item.route)" v-for="(item, index) in common.profileMenu" :key="index">
                <i v-bind:class="item.icon"></i>
                <label>{{item.title}}</label>
              </span>
              <span class="dropdown-item dropdown-item-menu-title">
                <label>Documents</label>
              </span>
              <!--GUIDE-->
              <!-- <span class="dropdown-item" @click="openModal('#guideModal')">
                <i class="far fa-question-circle"></i>
                <label>Guide</label>
              </span> -->
              <!--PRIVACY POLICY-->
              <span class="dropdown-item" @click="openModal('#privacyModal')">
                <i class="fas fa-shield-alt"></i>
                <label>Privacy Policy</label>
              </span>            
              <!--TERMS AND CONDITIONS-->
              <span class="dropdown-item" @click="openModal('#termsAndConditionsModal')">
                <i class="fa fa-handshake-o"></i>
                <label>Terms and Conditions</label>
              </span>
              <span class="dropdown-item" v-on:click="logOut()">
                <i class="fas fa-sign-out-alt"></i>
                  <label>Logout</label>
                </span>
            </span>
          </span>
        </div>

    <div class="dropdown"> 
          <span class="nav-item" v-bind:class="{'active-menu': settingFlag === true}" data-toggle="dropdown" id="settings" aria-haspopup="true" aria-expanded="false" v-on:click="makeActive('dropdown')" v-bind:onkeypress="makeActive('')">
            <span>
              <i class="far fa-bell"></i>
            </span>
            <span class="dropdown-menu dropdown-menu-right" aria-labelledby="settings">
              <span v-for="item, index in data">
                <i style="font-size:100%;" class="far fa-bell">
                {{item.display}} <p>{{item.created_at_human}}</p></i>
              </span>
            </span>
          </span>
        </div>     

        <div class="dropdown" v-if="user.messages.data !== null"> 
            <span class="nav-item" data-toggle="dropdown" id="notifications" aria-haspopup="true" aria-expanded="false">
              <span>
                <i class="fas fa-envelope" style="font-size: 22px;margin-top: 2px;"></i>
                <label class="badge badge-danger" style="margin-left: -15px;" v-if="parseInt(user.messages.totalUnreadMessages) > 0">{{user.messages.totalUnreadMessages}}</label>
              </span>
              <span class="dropdown-menu dropdown-menu-right dropdown-menu-notification" aria-labelledby="notifications">
                <span class="notification-header" @click="redirect('/' + common.messagesHeader.path)">
                  Recent
                  <label class="badge badge-danger">{{user.messages.totalUnreadMessages}}</label>
                </span>
                <span class="notification-item" v-for="item, index in user.messages.data" v-if="user.messages.data !== null" @click="redirect('/' + common.messagesHeader.path + '/' + item.payload, item)">
                  <span class="notification-title">
                    {{item.title.username}}
                    <label class="badge badge-danger" style="margin-left: 5px;" v-if="parseInt(item.total_unread_messages) > 0">{{item.total_unread_messages}}</label>
                  </span>
                  <span class="notification-description">{{item.description}}</span>
                  <span class="notification-date">Posted on {{item.created_at_human}}</span>
                </span>
              </span>
            </span>
        </div>


        <div class="dropdown" v-if="user.notifications.data !== null"> 
            <span class="nav-item" v-bind:class="{'active-menu': notifFlag === true}" data-toggle="dropdown" id="notifications" aria-haspopup="true" aria-expanded="false" v-on:click="makeActive('notif')" v-bind:onkeypress="makeActive('')" v-if="user.notifications.data !== null">
              <span>
                <i class="fa fa-bell"></i>
                <label class="notifications badge-danger" v-if="parseInt(user.notifications.current) > 0">{{user.notifications.current}}</label>
              </span>
              <span class="dropdown-menu dropdown-menu-right dropdown-menu-notification" aria-labelledby="notifications">
                <span class="notification-header">
                  Notifications
                </span>
                <span class="notification-item" v-for="item, index in user.notifications.data" v-if="user.notifications.data !== null && item.status !== 'ac_viewed'" v-on:click="updateNotification(item, user.notifications.current, index)" v-bind:class="{'notification-item-unread': index < user.notifications.current}">
                  <span class="notification-title">
                    {{item.title}}
                  </span>
                  <span class="notification-description">{{item.description}}</span>
                  <span class="notification-date">Posted on {{item.created_at_human}}</span>
                </span>
              </span>
            </span>
        </div>

      </span>

    </nav>

         <!-- Confirmation Modal -->
    <div class="modal" id="timerHeaderModal" v-if="confirmation.message !== null">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h4 class="modal-title"><i class="fa fa-warning text-danger"></i> Confirmation!</h4>
            <button type="button" class="close" data-dismiss="modal">&times;</button>
          </div>
          <div class="modal-body">
            {{confirmation.message}}
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-primary" data-dismiss="modal">Ok</button>
          </div>
        </div>
      </div>
    </div>

    <div>
   </div>
  </div>
</template>
<script>
import ROUTER from 'src/router'
import AUTH from 'src/services/auth'
import CONFIG from 'src/config.js'
import COMMON from 'src/common.js'
import Echo from 'laravel-echo'
import Vue from 'vue'
export default {
  mounted(){
    console.log(this.user)
    if(COMMON.broadcastingFlag === true){
      this.initPusher()
    }
  },
  data(){
    return{
      user: AUTH.user,
      data: null,
      tokenData: AUTH.tokenData,
      settingFlag: false,
      menuFlag: false,
      notifFlag: false,
      config: CONFIG,
      confirmation: {
        message: null,
        action: null
      },
      sort: {
        column: 'created_at',
        value: 'desc'
      },
      accountNotif: null,
      common: COMMON
    }
  },
  methods: {
    makeActive(icon){
      if(icon === 'dropdown'){
        this.settingFlag = true
        this.menuFlag = false
        this.notifFlag = false
      }else if(icon === 'sidebar'){
        this.settingFlag = false
        this.menuFlag = true
        this.notifFlag = false
      }else if(icon === 'notif'){
        this.settingFlag = false
        this.menuFlag = false
        this.notifFlag = true
      }else{
        this.settingFlag = false
        this.menuFlag = false
        this.notifFlag = false
      }
    },
    logOut(){
      AUTH.deaunthenticate()
    },
    redirect(parameter, item = null){
      if(item === null){
        AUTH.redirect(parameter)
      }else{
        this.updateMessages(parameter, item)
      }
    },
    display(){
    },
    initPusher(){
      console.log('hi')
      if(CONFIG.PUSHER.flag === 'pusher'){
        window.Echo = new Echo({
          broadcaster: 'pusher',
          key: CONFIG.PUSHER.key,
          cluster: 'ap1',
          encrypted: true
        })
      }else{
        window.Echo = new Echo({
          broadcaster: 'pusher',
          key: CONFIG.PUSHER.key,
          wsHost: CONFIG.PUSHER.wsHost,
          wsPort: CONFIG.PUSHER.wsPort,
          disableStats: true,
          enabledTransports: ['ws', 'wss']
        })
      }
      window.Echo.channel(COMMON.pusher.channel)
      .listen('call', e => {
        console.log(e)
      })
      .listen(COMMON.pusher.notifications, e => {
        AUTH.addNotification(e.data)
      })
      .listen(COMMON.pusher.messages, e => {
        AUTH.addMessage(e.data)
      })
      .listen(COMMON.pusher.messageGroup, e => {
        if(parseInt(e.data.id) === AUTH.messenger.messengerGroupId){
          console.log('group', e.data)
          AUTH.messenger.group.status = parseInt(e.data.status)
          AUTH.messenger.group.validations = e.data.validations
          AUTH.messenger.group.rating = e.data.rating
          AUTH.messenger.group.created_at_human = e.data.created_at_human
          AUTH.playNotificationSound()
          if(e.data.message_update === true){
            // update messages
            this.retrieveMessages(parseInt(e.data.id))
          }
        }
      })
    },
    retrieveMessages(id){
      let parameter = {
        condition: [{
          value: id,
          column: 'messenger_group_id',
          clause: '='
        }],
        sort: {
          'created_at': 'ASC'
        }
      }
      this.APIRequest('messenger_messages/retrieve', parameter).done(response => {
        if(response.data.length > 0){
          AUTH.messenger.messages = response.data
        }else{
          AUTH.messenger.messages = null
        }
      })
    },
    openModal(id){
      $('#profileModal').modal('hide')
      $('#guideModal').modal('hide')
      $('#privacyModal').modal('hide')
      $('#termsAndConditionsModal').modal('hide')
      setTimeout(() => {
        $(id).modal('show')
      }, 100)
    },
    updateNotification(item, current, index){
      if(parseInt(current) > index){
        let parameter = {
          id: item.id
        }
        this.APIRequest('notifications/update', parameter).then(response => {
          AUTH.retrieveNotifications(this.user.userID)
          this.redirect(item.route)
        })
      }else{
        this.redirect(item.route)
      }
    },
    updateMessages(params, item){
      if(item.total_unread_messages > 0){
        let parameter = {
          messenger_group_id: item.messenger_group_id
        }
        this.APIRequest('messenger_messages/update_by_status', parameter).then(response => {
          AUTH.redirect(params)
        })
        item.total_unread_messages = 0
      }else{
        AUTH.redirect(params)
      }
    }
  }
}
</script>
<style scoped lang="scss">
@import "~assets/style/colors.scss";
/*
        BODY
*/
body{
  font-size: 13px;
  font-weight: 400;
}
.btn{ 
  font-size: 12px;
}
.btn:hover{
  cursor: pointer;
}
.account-picture i{
  font-size: 100px !important;
}
.arrow::before{
  border-right-color: #ed2a2a !important;
  position: relative;
  border-top-color: #ed2a2a !important;
}
/*------------------------------------
          TABLES
--------------------------------------*/
.table{
  font-size: 12px;
}
/*----------------------------------------
            Forms
------------------------------------------*/
.form-control{
  height: 35px;
  font-size: 12px;
}
  .system-header{
    float: left;
    height: 50px;
    font-size: 24px;
    width: 18%;
    background: $primary;
    text-align: center;
    position: fixed;
  }
  
  .header-navbar{
    background: $primary;
    height: 50px;
    float: left;
    width: 82%;
    position: fixed;
    margin-left: 18%;
    z-index: 5000;
  }
  /*-- navbar --*/
  .system-header .navbar-brand{
    color: #fff;
    text-transform: uppercase;
    line-height: 30px;
  }
  
/*---------------------------------------------
 
        HEADER ACCOUNT PROFILE
-----------------------------------------------*/
  .header-navbar-nav{
      height: 50px;
      float: left;
      color: #fff;
      width: 15%;
  }
  .header-navbar-nav label{
    font-size: 13px;
    font-weight: 500;
    vertical-align: middle;
    padding-left: 10px;
  }
  .header-navbar-nav i{
    font-size: 24px;
     padding: 10px 0 0 5%;
  }
  .header-navbar-nav:hover{
    cursor: pointer;
    background: $primary;
  }
/*---------------------------------------------
       
                  ICONS
-----------------------------------------------*/
#messagesHeader{
  margin-left: 70%;
}
.nav-item{
  width: 15%;
  height: 50px;
  text-align: center;
  float: right;
  color: #fff;
  display: inline;
  padding-top: 0px !important;
}
.left-menu-icons, right-menu-icons{
  height: 50px;
  float: left;
  width: 50%;
  position: fixed;
  z-index: 100;
}
.nav-item span i{
  padding: 12px 0 15px 0;
  font-size: 24px;
}
.nav-item span .notifications{
  color: #ffffff;
  border-radius: 5px;
  height: 18px;
  width: 18px;
  margin-left: -10px;
  font-size: 10px;
  margin-top: 15px;
  text-align: center;
  padding-top: 2px;
}
.nav-item span .notifications:hover{
  cursor: pointer;
}
.nav-item .label{
  background: #ff0000;
  padding: 5px;
  margin: -10px 0 0 -10px;
  border-radius: 2px;
  border-color: solid 1px #ff0000;
  position: fixed;
  z-index: 1;
}
.nav-item:hover{
  background: $darkPrimary;
  cursor: pointer;
}
.dropdown-menu{
  width: 250px;
  min-height: 250px;
  border-radius: 0px !important;
  padding-bottom: 0px !important;
}
.dropdown-menu-notification{
  padding-top: 0px !important;
  width: 350px !important;
  height: 400px !important;
  overflow-y: auto;
}
.dropdown-item{
  width: 100% !important;
  height: 40px !important;
  float: left !important;
  background: #fff !important;
  padding-top: 0px !important;
}
.dropdown-item:hover{
  background: #ddd !important;
}
.dropdown-item i{
  font-size: 14px !important;
  padding-right: 10px !important;
  color: $primary !important;
}
.dropdown-item label{
  font-size: 14px !important;
}
.dropdown-item label:hover, .dropdown-item i:hover{
  cursor: pointer;
}
.dropdown-header{
  padding: 5px 0 10px 0;
  width: 100%;
  text-align: center;
  border-bottom: solid 1px #ccc;
}
.dropdown-item-profile{
  height: auto;
  width: 100%;
  float: left;
  top: 0;
}
.dropdown-item-menu-title label{
  line-height: 40px;
  font-weight: 550;
}
.dropdown-item-menu-title:hover, .dropdown-item-menu-title label:hover{
  cursor: default;
  background: #fff !important;
}
#account{
  background: #fff;
}
.account-picture{
  height: auto;
  width: 100%;
  float: left;
  padding-bottom: 12px;
}
.account-info{
  width: 100%;
  display: inline-block;
  float: left;
  font-weight: 550;
  color: $primary;
  margin-top: 25px;
}
.dropdown-item-button{
  height: 50px;
  width: 100%;
  float: left;
  background: #fff;
  border-top: solid 1px #ddd;
}
.dropdown-item-button button{
  height: 40px;
  border-radius: 0;
  width: 100px;
  margin-top: 5px;  
}
.dropdown-item-button button:hover{
  background: #ff0000;
  color: #fff;
}
/*---------------------------------------------
 
 
        HEADER TOGGLER MENU
-----------------------------------------------*/
.navbar-menu-toggler-md{
  height: 50px;
  float: left;
  text-align: center;
  font-size: 16px;
  color: #fff;
  line-height: 50px;
  display: none;
}
.navbar-menu-toggler-md:hover{
  cursor: pointer;
  background: $primary;
}
.active-menu{
  background: $primary;
}
/*--------------------------------------
          PROFILE PICTURE
  
---------------------------------------*/
.profile-photo-header{
  float: left;
  width: 100%;
  height: 80px;
  color: $primary;
}
.profile-image-holder-header{
  width: 100%;
  float: left;
  height: 80px;
  text-align: center;
  margin-top: 25px;
}
.profile-image-holder-header img{
  width: 80px;
  height: 80px;
  border-radius: 50%;
}
.profile-photo-header i{
  float: left !important;
  font-size: 60px !important;
  line-height: 80px !important;
  width: 100% !important;
}
.logo-brand{
  width: 100px;
  height: 100%;
  cursor: pointer;
  background-size: auto;
}
.left-menu-icons .account-type{
  padding: 10px 10px 10px 10px;
  margin-top: 6px;
  margin-left: 5px;
  border-radius: 5px;
  font-size: 11px;
  color: #fff;
  float: left;
}
.semester-selector{
  float: left;
  margin-top: 6px;
}
.semester-selector button{
  height: 36px !important;
  margin-left: 5px;
}
.semester-selector .dropdown-menu{
  min-height: 100px !important;
  overflow-y: hidden;
  margin-top: 7px;
  font-size: 14px !important;
  text-align: left;
}
.semester-selector .dropdown-menu .dropdown-item{
  padding: 0px !important;
  line-height: 30px !important;
}
.semester-selector .dropdown-menu .dropdown-item i{
  padding-left: 5px;
  padding-right: 5px;
}
.semester-selector .dropdown-menu .dropdown-item:hover{
  cursor: pointer;
}
/*
      NOTIFICATION DROPDOWN
*/
.notification-header{
  width: 100%;
  float: left;
  border-bottom: solid 1px #ddd;
  padding-top: 10px;
  padding-bottom: 10px;
  text-align: center;
}
.notification-item{
  width: 100%;
  float: left;
  border-bottom: solid 1px #ddd;
}
.notification-item:hover{
  cursor: pointer;
  background: #efefef;
}
.notification-item-unread{
  background: #efefef;
}
.notification-title{
  width: 96%;
  margin-right: 2%;
  margin-left: 2%;
  float: left;
  font-size: 12px;
  padding-top: 5px;
  padding-bottom: 5px;
  font-weight: 600;
}
.notification-description{
  width: 96%;
  margin-right: 2%;
  margin-left: 2%;
  float: left;
  color: #999;
  font-size: 12px;
  text-align: justify;
}
.notification-date{
  width: 96%;
  margin-right: 2%;
  margin-left: 2%;
  float: left;
  color: #999;
  font-size: 11px;
  text-align: justify;
  margin-bottom: 5px; 
}
/*---------------------------------------------------------          
                  RESPONSIVE HANDLER
-----------------------------------------------------------*/
/*-------------- Medium and Large Screens for Tablets and Desktop --------------*/
 @media (min-width: 1200px){
    .system-header{
      width: 18%;
    }
    .header-navbar{
      width: 82%;
      margin-left: 18%;
    }
    .header-navbar-nav{
      width: 15% !important;
    }
    .navbar-menu-toggler-md{
      display: none;
    }
    .left-menu-icons{
      width: 40% !important;
    }
    .right-menu-icons{
      width: 60% !important;
    }
    .nav-item{
      width: 5% !important;
    }
  }
 @media (max-width: 1199px){
    .system-header{
      width: 18%;
    }
    .header-navbar{
      width: 82%;
      margin-left: 18%;
    }
    .header-navbar-nav{
      width: 30%;
    }
    .navbar-menu-toggler-md{
      display: none;
    }
    .left-menu-icons{
      width: 60% !important;
    }
    .right-menu-icons{
      width: 40%;
    }
    .nav-item{
      width: 10%;
    }
  }
@media (max-width: 991px){
   .system-header{
      width: 30%;
    }
    .header-navbar{
      width: 70%;
      margin-left: 30%;
    }
   .header-navbar-nav{
      width: 30%;
    }
    .navbar-menu-toggler-md{
      width: 10%;
      text-align: center;
      display: block;
    }
    .left-menu-icons{
      width: 30% !important;
    }
    .right-menu-icons{
      width: 70% !important;
    }
    .nav-item{
      width: 10%;
    }
 }
/*-------------- Small Screen for Mobile Phones --------------*/
 @media (max-width: 767px){
    .header-margin{
     width: 10% !important;
    }
    
    .system-header{
      width: 15%;
    }
    
    .navbar-brand{
      width: 100% !important;
      margin: 0px !important;
    }
    
    .header-navbar{
      width: 85%;
      margin-left: 15%;
    }
    .nav-item{
      width: 20%;
    }
    .logo-brand{
      width: 50px;
      height: 50px;
    }
    .left-menu-icons{
      width: 20% !important;
    }
    .right-menu-icons{
      width: 80% !important;
    }
    .hide-on-mobile{
      display: none !important;
    }
  }
  @media (max-width: 300px){
    .logo-brand{
      width: 50px;
      height: 50px;
    }
  }
</style>
