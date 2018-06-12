<template>
  <div class="hello">
    <h1>{{ msg }}</h1>

    <input type="text"
           v-model="search"
           @keyup.enter="getGitRepos"
           class="form-control" placeholder="Search repository">
    <button type="button"
            @click="getGitRepos"
            class="btn btn-primary btn-block inactive">Go!</button>

    <div class="wrapper">
      <div v-for="repo in repos"
           :key="repo.full_name"
           class="card">
        <a :href="repo.html_url" target="_blank">

          <b>{{repo.name}}</b>

          <img :src="repo.owner.avatar_url" />
          {{repo.owner.login}}

        </a>

        <rating-stars :repository="repo"
                      :rated-repositories="ratesCollection"
                      @clicked="onClickStar"></rating-stars>
      </div>
    </div>

  </div>
</template>

<script>
import axios from 'axios';
import RatingStars from './RatingStars.vue';

export default {
  name: 'HelloWorld',

  components: {
    RatingStars,
  },

  data() {
    return {
      repos: [],
      search: '',
      ratesCollection: [],
      msg: 'Rate public repos',
    };
  },

  methods: {
    getGitRepos() {
      let strQuery = this.search.trim();

      if (strQuery.length < 1) {
        alert('Type at least 3 chars');
      } else {
        strQuery = strQuery.toLowerCase();
        axios.get('https://api.github.com/search/repositories?q=' + strQuery + '&per_page=12').then((response) => {
          this.repos = response.data.items;
        }).catch((e) => {
          this.repos.push(e);
        });
      }
    },

    onClickStar(value) {
      this.ratesCollection.push(value);
      localStorage.setItem('ratesCollection', JSON.stringify(this.ratesCollection));
    },
  },

  mounted() {
    if (localStorage.getItem('ratesCollection')) {
      this.ratesCollection = JSON.parse(localStorage.getItem('ratesCollection'));
    }
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.wrapper {
  display: flex;
  max-width: 60vw;
  flex-flow: row wrap;
  flex: 0 0 100%;
  padding-top: 12px;
  margin: 0 auto;
}
.card {
  box-shadow: rgba(0, 0, 0, 0.117647) 0px 1px 6px, rgba(0, 0, 0, 0.117647) 0px 1px 4px;
  margin: 12px;
  transition: .15s all ease-in-out;
}
.card:hover {
   transform: scale(1.1);
 }
.card a {
  text-decoration: none;
  padding: 12px;
  color: #03A9F4;
  font-size: 14px;
  display: flex;
  flex-direction: column;
  align-items: center;
}
.card img {
  height: 100px;
}
.card small {
  font-size: 10px;
  padding: 4px;
}
</style>
