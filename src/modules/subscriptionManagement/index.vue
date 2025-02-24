<template>
    <div class="container">
      <div v-if="user.merchant != null">
          <div class="row" style="width: 100%;">
            <div class="column" style="width: 80%;">
              <h3>Subscriptions Management</h3>
              <p style="margin-top: 5px;">Here are the latest update of subscriptions as of the moment.</p>
            </div>
          </div>
          <div class="mt-4">
            <p style="color: black; margin: 0; font-size: 17px; margin-top: 30px"><b>List of Subscribers</b></p>
            <p style="margin-top: 5px;">The following data shows the list of subscribers.</p>
          </div>
            <br>
            <div class="mt-4">
                <basic-filter
                    v-bind:category="category"
                    :activeCategoryIndex="0"
                    :activeSortingIndex="0"
                    @changeSortEvent="retrieve($event.sort, $event.filter)"
                    @changeStyle="manageGrid($event)"
                    :grid="['list', 'th-large']">
                </basic-filter>
            </div>
            <div class="table-container">
              <table class="table table-bordered table-responsive" v-if="data.length > 0">
                <thead>
                  <tr>
                      <td class="header"><b>Username</b></td>
                      <td class="header"><b>Full Name</b></td>
                      <td class="header"><b>Address</b></td>
                      <td class="header"><b>Total Donations</b></td>
                  </tr>
                  </thead>
                  <tbody>
                  <tr v-for="(item, index) in data" :key="index">
                    <td class="header">{{item.username}}</td>
                    <td class="header">{{item.first_name}} {{item.last_name}}</td>
                    <td class="header">{{item.address}}</td>
                    <td class="header">{{item.total_amount}}</td>
                  </tr>
                </tbody>
              </table>
              <empty v-if="data.length === 0" :title="'No Subscriptions available!'" :action="'Keep growing.'"></empty>
            </div>
          <Pager
          :pages="numPages"
          :active="activePage"
          :limit="limit"
          v-if="data.length > 0"
        />
        <div class="mt-5 pt-5">
          <p style="color: black; margin: 0; font-size: 17px;"><b>Subscribers Graph</b></p>
          <p style="margin-top: 5px;">The following data shows status of subscribers.</p>
        </div>
        <div class="graph">
          <GraphHeader @temp="headSub" :data="graphSub" :name="'Subscription'"/>
          <BarGraph v-if="graphSubscribe.labels.length > 0" :data="graphSub"/>
        </div>
        <div class="mt-4">
          <p style="color: black; margin: 0; font-size: 17px;"><b>Donations Graph</b></p>
          <p style="margin-top: 5px;">The following data shows status of donations.</p>
        </div>
        <div class="graph">
          <GraphHeader @temp="headDonate" :data="graphDon" :name="'Donations'"/>
          <BarGraph v-if="graphDonations.labels.length > 0" :data="graphDon"/>
        </div>
      </div>
      <div v-else>
        <empty v-if="data.length === 0" :title="'No Church available!'" :action="'Keep growing.'"></empty>
      </div>
  </div>
</template>

<script>
import ROUTER from 'src/router'
import AUTH from 'src/services/auth'
import CONFIG from 'src/config.js'
import Pager from 'src/modules/generic/Pager.vue'
import BarGraph from 'src/modules/generic/BarGraph.vue'
import GraphHeader from 'src/modules/generic/HeaderGraph.vue'
export default{
  mounted(){
    if(this.user.merchant !== null){
      this.retrieve({'T1.username': 'desc'}, {column: 'username', value: ''})
      this.donationGraph(this.donateSelected)
      this.subscriptionGraph(this.subSelected)
    }
  },
  data(){
    return {
      user: AUTH.user,
      data: [],
      auth: AUTH,
      config: CONFIG,
      category: [{
        title: 'Sort by',
        sorting: [{
          title: 'Username Ascending',
          payload: 'T1.username',
          payload_value: 'asc'
        }, {
          title: 'Username Descending',
          payload: 'T1.username',
          payload_value: 'desc'
        }, {
          title: 'Full Name Ascending',
          payload: 'name',
          payload_value: 'asc'
        }, {
          title: 'Full Name Descending',
          payload: 'name',
          payload_value: 'desc'
        }, {
          title: 'Address Ascending',
          payload: 'T2.address',
          payload_value: 'asc'
        }, {
          title: 'Address Descending',
          payload: 'T2.address',
          payload_value: 'desc'
        }]
      }],
      filter: null,
      sort: null,
      limit: 5,
      numPages: null,
      activePage: 1,
      donateSelected: null,
      subSelected: null,
      graphSubscribe: {
        labels: [],
        datasets: [
          {
            fill: true,
            borderColor: '#f87979',
            backgroundColor: '#56C596',
            label: 'SUBSCRIBERS',
            data: []
          }
        ]
      },
      graphDonations: {
        labels: [],
        datasets: [
          {
            fill: false,
            borderColor: '#f87979',
            backgroundColor: '#56C596',
            label: 'AMOUNT OF DONATIONS',
            data: []
          }
        ]
      }
    }
  },
  computed: {
    graphDon(){
      return this.graphDonations
    },
    graphSub(){
      return this.graphSubscribe
    }
  },
  components: {
    'empty': require('components/increment/generic/empty/Empty.vue'),
    'basic-filter': require('modules/generic/Basic.vue'),
    'increment-modal': require('components/increment/generic/modal/Modal.vue'),
    Pager,
    BarGraph,
    GraphHeader
  },
  methods: {
    redirect(route){
      ROUTER.push(route)
    },
    retrieve(sort, filter){
      if(sort !== null){
        this.sort = sort
      }
      if(filter !== null){
        this.filter = filter
      }
      if(sort === null && this.sort !== null){
        sort = this.sort
      }
      if(filter === null && this.filter !== null){
        filter = this.filter
      }
      let parameter = {
        condition: [{
          value: '%' + this.filter.value + '%',
          column: this.filter.column,
          clause: 'like'
        }],
        merchant: this.user.merchant.id,
        sort: sort,
        limit: this.limit,
        offset: (this.activePage > 0) ? ((this.activePage - 1) * this.limit) : this.activePage
      }
      this.APIRequest('subscriptions/retrieve_by_merchant', parameter).then(response => {
        if(response.data.length > 0){
          this.data = response.data
        }else{
          this.data = []
        }
      })
    },
    headDonate(e){
      this.donateSelected = e
      this.donationGraph(this.donateSelected)
    },
    donationGraph(e){
      this.donateSelected = e
      let parameter = {
        account_id: this.user.userID,
        date: this.donateSelected === null ? 'yearly' : this.donateSelected
      }
      $('#loading').css({display: 'block'})
      this.APIRequest('subscriptions/retrieve_subscription_graph', parameter).then(response => {
        console.log('[donation]', response)
        $('#loading').css({display: 'none'})
        if(response.data.dates.length > 0){
          this.graphDonations.labels = response.data.dates
          this.graphDonations.datasets[0].data = response.data.result
        }
      })
    },
    headSub(e){
      this.subSelected = e
      this.subscriptionGraph(this.subSelected)
    },
    subscriptionGraph(e){
      this.subSelected = e
      let parameter = {
        merchant_id: this.user.merchant.id,
        date: this.subSelected === null ? 'yearly' : this.subSelected
      }
      $('#loading').css({display: 'block'})
      this.APIRequest('subscriptions/retrieve_subscribers_graph', parameter).then(response => {
        console.log('[subscription]', parameter)
        $('#loading').css({display: 'none'})
        if(response.data.dates.length > 0){
          this.graphSubscribe.labels = response.data.dates
          this.graphSubscribe.datasets[0].data = response.data.result
        }
      })
    }
  }
}
</script>

<style scoped lang="scss">
@import "~assets/style/colors.scss";
.graph{
  margin-top: 3%;
  background-color: white;
  width: 100%;
  // height: 550px;
  border-radius: 7px;
  padding: 30px 30px 30px 30px;
  margin-bottom: 50px;
}
.icon-eye{
  color: $secondary;
  margin: 2px;
}
.icon-trash{
  color: red;
  margin: 2px;
}
.header {
  text-align: center;
}
.table{
  background-color: white;
  
}
.table-container{
  height: 50vh;
  background-color: white;
  margin-bottom: 10px;
  margin-top: 20px;
}
.title{
  color: rgb(202, 200, 200);
  margin: 0;
}
.container{
  width: 65%;
  margin-bottom: 100px;
  margin-top: 25px;
}

.first{
  height: 100px;
  width: 32%;
  background-color: $primary;
  margin-right: 2%;
  padding-top: 25px;
  text-align: center;
  border-radius: 5px;
}
.second{
  height: 100px;
  width: 32%;
  background-color: $danger;
  margin-right: 2%;
  padding-top: 25px;
  text-align: center;
  border-radius: 5px;
}
.third{
  height: 100px;
  width: 32%;
  background-color: $secondary;
  padding-top: 25px;
  text-align: center;
  border-radius: 5px;
}

@media (max-width: 992px){
  .container, .table{
    width: 100%;
  }
}
</style>
