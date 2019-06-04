<template>
  <div id="rw-steemit">
    <steem-connect :account="account"></steem-connect>
    <blog-feeds></blog-feeds>
  </div>
</template>

<script>
  const steemconnect = require('steemconnect');

  import BlogFeeds from "./components/BlogFeeds";
  import SteemConnect from "./components/SteemConnnect";

  export default {
    name: 'App',
    components: {
      SteemConnect,
      BlogFeeds
    },
    data() {
      return {
        client: null,
        account: {
          loginLink: '',
          access_token: '',
          username: '',
          id: '',
          balance: '',
          commentCount: '',
          currentRewards: '',
          created: ''
        },
      }
    },
    methods: {
      logOut() {
        const vm = this;
        this.client.revokeToken(function (err, res) {
          if (res && res.success) {
            vm.account.access_token = null;
          }
        });
      },
      setAccessToken() {
        this.client.setAccessToken(this.account.access_token);
      },
      getLoginLink() {
        this.account.loginLink = this.client.getLoginURL();
      },
      getUserDetails() {
        const vm = this;
        this.client.me(function (err, res) {
          if (res) {
            console.log(res);
            vm.account.id = res.account.id;
            vm.account.balance = res.account.balance;
            vm.account.commentCount = res.account.comment_count;
            vm.account.currentRewards = res.account.curation_rewards;
            vm.account.created = res.account.created;
          }
        });
      },
      initialize() {
        this.client = new steemconnect.Client({
          app: 'steemshopping',
          callbackURL: process.env.NODE_ENV === 'development' ? 'http://localhost:8080' : 'https://www.rewards.com/rwrd-steemit',
          accessToken: 'access_token',
          scope: ['vote', 'comment'],
        });
        this.getLoginLink();
        this.account.access_token = new URLSearchParams(document.location.search).get('access_token');
        this.account.username = new URLSearchParams(document.location.search).get('username');
        if (this.account.access_token) {
          this.setAccessToken();
          this.getUserDetails();
        }
      },
      vote(post) {
        const vm = this;
        this.client.vote(this.account.username, post.author, post.permalink, 10000, function (err, res) {
          console.log(err, res);
          if (!err) {
            window.alert('successfully voted to this post');
          }
        });
      }
    },
    mounted() {
      this.initialize();
      Vue.$on('logout:steemconnect', () => {
        this.logOut();
      });
      Vue.$on('vote:steemconnect', (post) => {
        this.vote(post);
      });

      // console.log(env);
    },
    beforeDestroy() {
      Vue.$off('logout:steemconnect');
      Vue.$off('vote:steemconnect');
    }
  }
</script>

<style>
</style>
