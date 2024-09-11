<!-- <script setup lang="ts">
defineProps<{
  msg: string
}>()
</script> -->

<script lang="ts">
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data() {
    return {
      theData: {},
      tempData: {},
      resultSet: [],
      tempResultSet: [],
      currentPage: 1,
      total: 0,
      imgURL: 'https://media.nfsacollection.net/',
      query: 'https://api.collection.nfsa.gov.au/search?limit=25&query=',
      searchString: 'lobby'
    }
  },
  methods: {
    fetchData() {
      let queryString = this.query + this.searchString + '&page=' + this.currentPage
      fetch(queryString)
        .then((response) => {
          response.json().then((res) => {
            this.$data.tempData = { ...this.$data.tempData, ...res }
            this.$data.tempResultSet = this.$data.tempResultSet.concat(res.results)
            this.$data.total = res.meta.count.total
            if (this.$data.total > 0) {
              if (this.currentPage * 25 < 500 && this.currentPage * 25 < this.$data.total) {
                this.currentPage++
                this.fetchData()
              } else {
                this.$data.theData = this.$data.tempData
                this.$data.tempData = {}
                this.$data.resultSet = this.$data.tempResultSet
                this.$data.tempResultSet = []
                this.currentPage = 1
              }
            } else {
              console.log('no results')
            }
          })
        })
        .catch((err) => {
          console.error(err)
        })
    }
  }
}
</script>

<template>
  <div class="greetings">
    <h1 class="green">{{ msg }}</h1>
    <button @click="fetchData">Click Me!</button>
    <ul class="list-v">
      <!-- create a variable called result, 
      loop through the API results and add a list item for each result.
      Use result to access properties like 'title' and 'name' -->
      <!-- <li v-for="result in theData.results" :key="result.title">
        {{ result.title }}
        {{ result.name }}
      </li> -->
    </ul>
  </div>
</template>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}
</style>
