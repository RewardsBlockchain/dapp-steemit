<template>
  <div id="rw-steemit">
    <steem-connect></steem-connect>
    <blog-feeds></blog-feeds>
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
