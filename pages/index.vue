<template>
<div>
  <h1>Hello World from Storyblok. Welcome to my first tool plugin.</h1>
  <p v-if="!loadingContext">
    You are editing "{{ story.name }}"
  </p>
</div>
</template>

<script>
import axios from 'axios'

export default {
  data() {
    return {
      story: {},
      loadingContext: true 
    }
  },

  mounted() {
    if (window.top === window.self) {
      window.location.assign('http://app.storyblok.com/oauth/tool_redirect')
    }

    window.addEventListener('message', this.processMessage, false)

    // Use getContext to get the current story 
    window.parent.postMessage({ action: 'tool-changed', tool: 'storyblok-zach@tool-plugin-z', event: 'getContext' }, 'https://app.storyblok.com')

    axios.get('/auth/user', { params: { space_id: this.$route.query.space_id }})
    .then((response) => {
      console.log(response.data)
    })

    axios.get(`/auth/spaces/${this.$route.query.space_id}/stories`)
    .then((response) => {
      console.log(response.data)
    })
  },

  methods: {
    processMessage(event) {
      if (event.data && event.data.action == 'get-context') {
        this.loadingContext = false 
        this.story = event.data.story 
      }
    }
  }
}
</script>
