<script>
import { getContent, RenderContent, isPreviewing } from '@builder.io/sdk-vue/vue3';
// import your NotFoundComponent
import NotFoundComponent from './components/NotFound.vue';

// define vue component
export default {
  name: 'App',
  // Use Builder's RenderContent to show your Builder content
  // Add NotFoundComponent to use in case of no content
  components: {
    RenderContent,
    NotFound: NotFoundComponent,
  },
  data: () => ({
    // boolean to control showing content
    canShowContent: false,
    // content variable to store content from Builder
    content: null,
    // use API key
    apiKey: "<API-KEY>", // <-- Replace with your Public API Key
  }),
  // mounted() lifecycle hook called after component is in the DOM  
  mounted() {
    // get page model from space (specified by the Public API Key)
    // and the URL
    getContent({
      model: 'page',
      apiKey: "<API-KEY>", // <-- Replace with your Public API Key
      userAttributes: {
        urlPath: window.location.pathname,
      },
    }).then(res => {
      // sets content to results of getContent()
      this.content = res;
      // If there's content or if the page is being viewed in the Visual Editor,
      // set canShowContent to true.
      this.canShowContent = this.content || isPreviewing();
      // If no content is found, set pageNotFound to true.
      // This is used in the template to conditionally render 404 component.
      if (!this.content) {
        this.pageNotFound = true;
      }
    });
  },
  computed: {
    // Provide a default title and default description in case there is no content
    computedTitle() {
      return this.content?.data?.title || 'Default title';
    },
    computedDescription() {
      return this.content?.data?.description || 'Default description';
    },
  },
}
</script>

<template>
<div>
  <Head>
  <!-- Make sure to give your page a title and description in Builder -->
    <title :title="computedTitle"></title>
    <meta name="description" :content="computedDescription" />
  </Head>
  <div class="header">
    <img src="./assets/logo.svg" alt="logo" width="50" height="50">
    </div> 
    <div class="content"> 
      <div v-if="canShowContent">
        <RenderContent
          model="page"
          :content="content"
          :api-key="apiKey"
        />
      </div>
      <NotFound v-if="pageNotFound" />
    </div> 
    <div class="footer"> 
      <p>DRAGONHACK 2023</p>
    </div>
</div>
</template>


<style lang="css">
.header {
    position: absolute;
    top: 0px;
    left: 0px;
    height: 50px;
    right: 0px;
    overflow: hidden;
    background-color: #42b883;
    text-align: right;
    padding-right: 20px;
}

.content {
    position: absolute;
    top: 50px;
    bottom: 50px;
    left: 0px;
    right: 0px;
    overflow: auto;
}

.footer {
    position: absolute;
    bottom: 0px;
    left: 0px;
    height: 50px;
    right: 0px;
    overflow: hidden;
    background-color: #35495e;
    text-align: center;
    color: whitesmoke;
}
</style>