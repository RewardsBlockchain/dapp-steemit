<template>
  <div>
    <template v-if="access_token">
      <h2><a href="#" @click="logOut">LogOut</a></h2>
      <div class="account-info">
        <p>My Steemit Account Information</p>
        <p>UserName: {{username}}</p>
        <p>ID: {{id}}</p>
        <p>Balance: {{balance}}</p>
        <p>Comment Count: {{commentCount}}</p>
        <p>Current Rewards: {{currentRewards}}</p>
        <p>Created: {{created}}</p>
      </div>
    </template>
    <h2 v-else><a :href="loginLink">Login to Steemit</a></h2>
  </div>
</template>

<script>
  const sc2 = require('steemconnect');

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
        this.api.revokeToken(function (err, res) {
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
        this.api.me(function (err, res) {
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
          // callbackURL: 'https://www.rewards.com/rwrd-steemit',
          callbackURL: 'http://localhost:8080',
          accessToken: 'access_token',
          scope: ['vote', 'comment'],
        });
        this.getLoginLink();
        this.access_token = new URLSearchParams(document.location.search).get('access_token');
        this.username = new URLSearchParams(document.location.search).get('username');
        if (this.access_token) {
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
  .account-info {
    border: 1px solid darkgray;
    padding: 20px;
  }
</style>
