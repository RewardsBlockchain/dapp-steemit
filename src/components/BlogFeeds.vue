<template>
  <div>
    <h2>Steemshoping Blogs</h2>
    <template v-if="posts.length > 0">
      <div class="list-group-item" v-for="(post, index) of posts" :key="index">
        <h4 class="list-group-item-heading">{{post.title}}</h4>
        <p>{{post.author}}
          <span class="view-details">
            <a href="#" @click="post.details = !post.details">{{post.details ? 'Close' : 'View'}} Details</a>
          </span>
        </p>
        <div v-if="post.details" v-html="post.body">
        </div>
        <img v-else :src="post.image"/>
        <p class="list-group-item-created">{{post.created}}</p>
      </div>
    </template>
    <template v-else>
      <div>
        loading posts
      </div>
    </template>
  </div>
</template>

<script>
  import {Client} from 'dsteem';

  const Remarkable = require('remarkable');

  const client = new Client('https://api.steemit.com');

  export default {
    name: "blog-feeds",
    data() {
      return {
        posts: [],
      }
    },
    methods: {
      fetchBlogs() {
        const vm = this
        const query = {
          tag: 'steemshopping',
          limit: 5,
        };
        client.database
          .getDiscussions('blog', query)
          .then(result => {
            result.forEach(post => {
              console.log(post)
              const md = new Remarkable({html: true, linkify: true});
              const json = JSON.parse(post.json_metadata);
              const image = json.image ? json.image[0] : '';
              const title = post.title;
              const author = post.author;
              const body = md.render(post.body);
              const permlink = post.permlink;
              const created = new Date(post.created).toDateString();
              vm.posts.push({
                title: title,
                author: author,
                image: image,
                body: body,
                created: created,
                details: false,
                permalink: permlink
              })
            });
          })
          .catch(err => {
            console.log(err);
          });
      }
    },
    mounted() {
      this.fetchBlogs()
    },
  }
</script>

<style scoped>
  .list-group-item {
    border: 1px solid darkgray;
    padding: 20px;
  }

  .list-group-item-created {
    text-align: right;
  }

  .view-details {
    float: right;
  }
</style>
