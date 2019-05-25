<template>
  <div>
    <template v-if="access_token">
        <div class="my-5">
          <b-link @click="logOut">LogOut</b-link>
        </div>
        <b-card>
          <b-card-body>
            <p>My Steemit Account Information</p>
            <p>UserName: {{username}}</p>
            <p>ID: {{id}}</p>
            <p>Balance: {{balance}}</p>
            <p>Comment Count: {{commentCount}}</p>
            <p>Current Rewards: {{currentRewards}}</p>
            <p>Created: {{created}}</p>
          </b-card-body>
        </b-card>
    </template>
    <template v-else>
      <div class="my-5">
        <a :href="loginLink">Login to Steemit</a>
      </div>
    </template>
  </div>
</template>

<script>
  const sc2 = require('sc2-sdk');

  export default {
    name: "steem-connect",
    data() {
      return {
        api: null,
        loginLink: '',
        access_token: '',
        username: '',
        id: '',
        balance: '',
        commentCount: '',
        currentRewards: '',
        created: ''
      }
    },
    methods: {
      logOut() {
        const vm = this;
        this.api.revokeToken(function(err, res) {
          if (res && res.success) {
            vm.access_token = null;
          }
        });
      },
      setAccessToken() {
        this.api.setAccessToken(this.access_token);
      },
      getLoginLink() {
        this.loginLink = this.api.getLoginURL();
      },
      getUserDetails() {
        const vm = this;
        this.api.me(function(err, res) {
          if (res) {
            console.log(res);
            vm.id = res.account.id;
            vm.balance = res.account.balance;
            vm.commentCount = res.account.comment_count;
            vm.currentRewards = res.account.curation_rewards;
            vm.created = res.account.created;
          }
        });
      },
      initialize() {
        this.api = sc2.Initialize({
          app: 'steemshopping',
          callbackURL: 'https://www.rewards.com/rwrd-steemit',
          accessToken: 'access_token',
          scope: ['vote', 'comment'],
        });
        this.getLoginLink();
        this.access_token = new URLSearchParams(document.location.search).get('access_token');
        this.username = new URLSearchParams(document.location.search).get('username');
        if(this.access_token) {
          this.setAccessToken();
          this.getUserDetails();
        }
      }
    },
    mounted() {
      this.initialize();
    }
  }
</script>

<style scoped>

</style>
