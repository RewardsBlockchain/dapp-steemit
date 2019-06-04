<template>
  <div>
    <template v-if="account && account.access_token">
      <h2><a href="#" @click="logOut">LogOut</a></h2>
      <div class="account-info">
        <p>My Steemit Account Information</p>
        <p>UserName: {{account.username}}</p>
        <p>ID: {{account.id}}</p>
        <p>Balance: {{account.balance}}</p>
        <p>Comment Count: {{account.commentCount}}</p>
        <p>Current Rewards: {{account.currentRewards}}</p>
        <p>Created: {{account.created}}</p>
      </div>
    </template>
    <h2 v-else><a :href="account.loginLink">Login to Steemit</a></h2>
  </div>
</template>

<script>
  export default {
    name: "steem-connect",
    props: {
      account: Object
    },
    data() {
      return {
      }
    },
    methods: {
      logOut() {
        Vue.$emit("logout:steemconnect");
      },
    }
  }
</script>

<style scoped>
  .account-info {
    border: 1px solid darkgray;
    padding: 20px;
  }
</style>
