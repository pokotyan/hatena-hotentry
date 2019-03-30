<template>
  <div class="container">
    <b-loading :is-full-page="isFullPage" :active.sync="isLoading" :can-cancel="true"></b-loading>
    <nav class="navbar" role="navigation" aria-label="main navigation">
      <div class="navbar-brand">
        <div class="navbar-item" v-on:click="setGenre('it')" v-bind:class="{ active: isActiveGenre('it') }">
          テクノロジー
        </div>
        <div class="navbar-item" v-on:click="setGenre('all')" v-bind:class="{ active: isActiveGenre('all') }">
          総合
        </div>
        <div class="navbar-item" v-on:click="setGenre('general')" v-bind:class="{ active: isActiveGenre('general') }">
          一般
        </div>
      </div>
    </nav>
    <div class="flex">
      <div class="flexItem" v-for="year in years" :key="year">
        <div v-on:click="setYear(year)" v-bind:class="{ active: isActiveYear(year) }">{{ year }}</div>
      </div>
    </div>
    <div class="panelContainer">
      <div v-for="hatenaLink in hatenaLinks" :key="hatenaLink.title">
        <b-collapse class="panel">
          <div class="panel-heading">
            <a :href="hatenaLink.Link"><div class="title">{{ `${hatenaLink.Date} ${hatenaLink.Title}` }}</div></a>
            <span> {{ hatenaLink.Users }}users</span>
          </div>
          <div class="panel-block">
            <div class="desc">
              {{ hatenaLink.Desc }}
            </div>
          </div>
        </b-collapse>
      </div>
    </div>
  </div>
</template>

<script>
import _ from 'lodash';
import dayjs from 'dayjs'
import { db } from '../firebase'
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data: () => ({
    hatenaLinks: [],
    years: [],
    currentGenre: null,
    currentYear: null,
    isLoading: false,
  }),
  created () {
    this.getAll()
  },
  methods: {
    setGenre(genre) {
      this.currentGenre = genre;
      this.getAll(genre);
    },
    setYear(year) {
      this.currentYear = year;
      this.hatenaLinks = this.hotEntry[year];
    },
    isActiveGenre(genre) {
      return this.currentGenre === genre;
    },
    isActiveYear(year) {
      return this.currentYear == year;
    },
    async getAll(genre = 'it') {
      this.isLoading = true;
      this.currentGenre = genre;
      let snapshot = await db.ref(`hatena/hot-entry/${this.currentGenre}`).once('value');
      let res = snapshot.val();

      res = Object.keys(res).map(date => ({ ...res[date], Date: date }));
      res = _.orderBy(res, ['Date'], ['desc']);

      const hotEntry = {}

      res.forEach(res => {
        const year = dayjs(res.Date).year()
        if (!hotEntry[year]) {
          hotEntry[year] = [];
        }
        hotEntry[year].push(res);
      })
      this.hotEntry = hotEntry;
      this.years = Object.keys(hotEntry);
      this.currentYear = Math.max(...this.years);
      this.hatenaLinks = hotEntry[this.currentYear];
      this.isLoading = false;
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.flex {
  display: flex;
  justify-content: flex-end;
}
.flexItem {
  padding-right: 10px;
}
.panelContainer {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}
.panel {
  width: 18vw;
  min-width: 230px;
  height: auto;
  margin-right: 10px;
  margin-bottom: 10px;
}
@media (max-width: 768px) {
  .panelContainer {
    justify-content: center;
  }
  .panel {
    min-width: 300px;
  }
}
.title {
  text-align: left;
  font-size: 20px;
}
.desc {
  text-align: left;
}
.active {
  color: red
}
</style>
