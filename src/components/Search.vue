<template>
  <div class="search-page">
    <search-bar></search-bar>
    <ul v-if="search.business">
      <li v-for="business in search.business">
        <br><b>{{business.name}}</b> {{business.rating}} ☆ </br>
        <br>{{business.reviews[0].text}} {{business.reviews[0].rating}} ☆ </br>
        <br>{{business.reviews[1].text}} {{business.reviews[1].rating}} ☆ </br>
      </li>
    </ul>
    <div class="loading" v-if="isLoading">
      <img src="../assets/spinner.gif"> Loading...
    </div>
  </div>
</template>

<script>
import SearchBar from "./SearchBar.vue";
import gql from "graphql-tag";
import { _ } from "vue-underscore";
import Router from "vue-router";

export default {
  name: "SearchPage",
  components: {
    SearchBar
  },
  apollo: {
    $loadingKey: "isLoading",
    search: {
      query() {
        if (this.term && this.location && this.radius && this.limit) {
          let date = new Date();
          console.log('** query() **', date.toLocaleTimeString())
          let q =  gql`
            query searchYelp(
              $term: String!
              $location: String!
              $radius: Float!
              $limit: Int!
            ) {
              search(
                term: $term
                location: $location
                radius: $radius
                limit: $limit
                sort_by: "rating"
              ) {
                total
                business {
                  name
                  rating
                  photos
                  id
                  reviews{
                    text
                    id
                    rating
                    time_created
                    url
                  }
                  categories {
                    title
                    alias
                  }
                }
              }
            }
          `;
          // console.log(q);
          return q;
        }
      },
      variables() {
        return {
          term: this.term,
          location: this.location,
          radius: this.radius * 1609.34,
          limit: +this.limit
        };
      },
    }
  },
  props: ["term", "location", "radius", "limit"],
  data() {
    return {
      search: {},
      isLoading: 0,
    };
  },
};
</script>

<!-- "scoped" attribute limits CSS to this component only -->
<style scoped>
h1,
h2 {
  font-weight: normal;
}

ul {
  list-style: none;
}
</style>
