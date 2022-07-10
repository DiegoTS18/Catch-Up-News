<template>
  <v-app theme="light">
    <v-app-bar app color="primary">
      <v-app-bar-nav-icon @click="drawer = !drawer" class="white--text"></v-app-bar-nav-icon>
      <v-app-bar-title>Catch Up</v-app-bar-title>
    </v-app-bar>
    <side-menu :drawer="drawer" v-on:source-selected="setSource"></side-menu>
    <v-main>
      <v-container fluid>
        <unavailable-content v-if="errors.count>0" :errors="errors"></unavailable-content>
        <main-content v-else :articles="articles"></main-content>
      </v-container>
      <v-footer color="primary" >
        <footer-content></footer-content>
      </v-footer>
    </v-main>
  </v-app>
</template>

<script>
import MainContent from "./components/main-content.vue";
import {NewsApiService} from "./core/services/news-api.service";
import SideMenu from "./components/side-menu.vue";
import FooterContent from "./components/footer-content.vue";

export default {
  name: 'App',
  components: {FooterContent, SideMenu, MainContent},
  data() {
    return{
      drawer: false,
      apikey:'8f01766a8baa41349eecc520ee0cb40d',
      articles:[],
      errors:[],
      newsApi: new NewsApiService()
    };
  },
  created() {//created es la funcion para ejecutarse al momento de iniciar
    console.log('Created');
    this.getArticlesForSource('bbc-news');
  },
  methods:{

    setSource(source){
      this.drawer=!this.drawer;
      this.getArticlesForSourceWithLogo(source)
    },

    getArticlesForSource(sourceId){
      this.newsApi.getArticlesForSource(sourceId)
          .then(response=>{
            this.articles=response.data.articles;//dentro de la documentacion encontramos la referencia de uso a .data
            console.log(response.data);
          })
          .catch(error=>{
            this.errors.push(error);
            console.log(this.errors);
          })
    },
    getArticlesForSourceWithLogo(source){
      this.newsApi.getArticlesForSource(source.id)
          .then(response=>{
            this.articles=response.data.articles;//dentro de la documentacion encontramos la referencia de uso a .data
            this.articles.forEach(article=>article.source.url=source.url);
            console.log(response.data);
          })
          .catch(error=>{
            this.errors.push(error);
            console.log(this.errors);
          })
    }
  }
}
</script>

