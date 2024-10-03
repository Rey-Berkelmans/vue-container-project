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
      query: 'https://api.collection.nfsa.gov.au/search?limit=25&hasMedia=yes&query=',
      searchString: 'Home Movie'
    }
  },

  methods: {
    fetchData() {
      let queryString = this.query + this.searchString + '&page=' + this.currentPage
      console.log('API call: ' + queryString)
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
                console.log('Pages: ' + Math.ceil(this.$data.total / 25))
                console.log('finished')
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
  <div class="search">
    <h1 class="green">{{ msg }}</h1>

    <input v-model="searchString" placeholder="query" />
    <button @click="fetchData">fetch data</button>

    <p>Total: {{ total }}</p>

    <ul role="list" class="list-v">
      <li v-for="(result, index) in resultSet" :key="result[index]">
        <p>{{ result['title'] }}</p>
        <p>{{ result['name'] }}</p>
        <img
          v-if="result['preview'] && result['preview'][0]"
          v-bind:src="imgURL + result['preview'][0]['filePath']"
          v-bind:alt="result['name']"
          v-bind:title="result['name']"
        />
        <video width="320" height="240" controls>
          <source
            v-if="result['preview'] && result['preview'][0]"
            v-bind:src="imgURL + result['preview'][0]['filePath']"
            type="video/mp4"
          />
        </video>
      </li>
    </ul>
  </div>
</template>

<style scoped>
.v-enter-from {
  opacity: 0;
  translate: -100px 0;
}
.v-enter-to {
  opacity: 1;
  translate: 0 0;
}
.v-leave-from {
  opacity: 1;
  translate: 0 0;
}
.v-leave-to {
  opacity: 0;
  translate: 100px 0;
}
img {
  display: inline-block;
  max-width: 100%;
  transition: all 2s;
  display: block;
  margin-left: auto;
  margin-right: auto;
}

video {
  display: inline-block;
  max-width: 100%;
  transition: all 2s;
  display: block;
  margin-left: auto;
  margin-right: auto;
}

ul {
  list-style: none;
  padding: 10px;
  display: flex;
  flex-wrap: wrap;
  gap: 1rem;
  justify-content: center;
  align-items: center;
}

ul li {
  display: inline;
  max-width: 400px;
  padding: 1rem;
  border: 3px solid #85bdbf;
  background-color: #000000;
}

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
  text-align: left;
}

/* @media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
} */
</style>
