<template>
  <div id="rw-steemit">
    <div class="container">
      <b-row>
        <b-col md="12">
          <steem-connect></steem-connect>
        </b-col>
      </b-row>
      <b-row class="mt-5">
        <b-col md="12">
          <blog-feeds></blog-feeds>
        </b-col>
      </b-row>
    </div>
  </div>
</template>

<script>

  import BlogFeeds from "./components/BlogFeeds";
  import SteemConnect from "./components/SteemConnnect";

  export default {
    name: 'App',
    components: {
      SteemConnect,
      BlogFeeds
    }
  }
</script>

<style>
</style>
