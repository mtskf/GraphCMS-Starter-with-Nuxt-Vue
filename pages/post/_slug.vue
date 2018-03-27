<template lang="pug">
  h2(v-if='loading > 0') Loading...
  div(v-else='')
    article
      h1 {{post.title}}
      .placeholder
        img(
          :alt='post.title',
          :src='`https://media.graphcms.com/resize=w:650,h:366,fit:crop/${post.coverImage.handle}`')
      vue-markdown {{post.content}}

</template>

<script>
  import gql from 'graphql-tag'
  import VueMarkdown from 'vue-markdown'

  const post = gql`
    query post($slug: String!) {
      post: Post(slug: $slug) {
        id
        slug
        title
        coverImage {
          handle
        }
        content
        dateAndTime
      }
    }
  `

  export default {
    name: 'PostPage',
    data: () => ({
      loading: 0,
      metaInfo: {
        title: '',
        desc: ''
      }
    }),
    head () {
      return {
        title: this.metaInfo.title,
        meta: [
          { property: 'og:title', content: this.metaInfo.title },
          { hid: 'description', name: 'description', content: this.metaInfo.desc }
        ]
      }
    },
    apollo: {
      $loadingKey: 'loading',
      post: {
        query: post,
        result (result) {
          this.metaInfo.title = result.data.post.title
          this.metaInfo.desc = result.data.post.content
        },
        prefetch: ({ route }) => {
          return {
            slug: route.params.slug
          }
        },
        variables () {
          return {
            slug: this.$route.params.slug
          }
        }
      }
    },
    components: { VueMarkdown }
  }
</script>

<style scoped lang="scss">
  .placeholder {
    height: 366px;
    background-color: #eee;
  }
</style>
