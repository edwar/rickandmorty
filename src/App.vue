<template>
  <div id="app">
    <div class="hero is-white is-gradient is-bold">
      <div class="hero-body">
        <h1 class="title">
          <span class="has-text-success">
            R&M
          </span>
          <span class="subtitle">
            Personajes
          </span>
        </h1>
        <div class="field has-addons is-pulled-right">
          <div class="control">
            <input v-model="search" type="text" class="input is-rounded" v-on:keyup.enter="searchData">
          </div>
          <div class="control">
            <button class="button is-success is-rounded" @click="searchData">Buscar</button>
          </div>
        </div>
      </div>
    </div>
    <div class="container">
      <div class="columns is-desktop is-mobile is-tablet is-multiline is-centered">
        <character @showModal="showModal" v-for="character of characters" :key="character.id" :character="character"/>
      </div>
      <nav class="pagination" role="navegation" aria-label="pagination">
        <a class="pagination-previous" @click="changepage(page - 1)">Anterior</a>
        <ul class="pagination-list">
          <li>
            <a class="pagination-link is-current">{{ page }}</a>
          </li>
        </ul>
        <a class="pagination-next" @click="changePage(page + 1)">Siguiente</a>
      </nav>
    </div>
    <div class="modal" :class="{ 'is-active' : modal }" v-if="modal">
      <div class="modal-background" @click="modal = false"></div>
      <div class="modal-card">
        <header class="modal-card-head">
          <p class="modal-card-title">{{characterDetail.name}}</p>
        </header>
        <div class="modal-card-body">
          <p v-if="characterDetail.gender">Genero:</p>
          <strong>{{characterDetail.gender}}</strong>
          <p v-if="characterDetail.species">Raza:</p>
          <strong>{{characterDetail.species}}</strong>
          <p v-if="characterDetail.status">Estado:</p>
          <strong>{{characterDetail.status}}</strong>
          <p v-if="characterDetail.type">Tipo:</p>
          <strong>{{characterDetail.type}}</strong>
        </div>
        <footer class="modal-card-foot">
          <button class="button is-danger" @click="modal = false">Cerrar</button>
        </footer>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import Character from './components/Character'

export default {
  name: 'app',
  components: {
    Character
  },
  data: function () {
    return {
      characters: [],
      page: 1,
      pages: 1,
      search: '',
      modal: false,
      characterDetail: {}
    };
  },
  created () {
    this.fetch()
  },
  methods: {
    fetch () {
      const params = {
        page: this.page,
        name: this.search
      }
      axios.get('https://rickandmortyapi.com/api/character/', { params })
        .then(res => {
          this.characters = res.data.results
          this.pages = res.data.info.pages
        })
        .catch(err => {throw new Error(err)})
    },
    changePage (page) {
      this.page = (page <= 0 || page > this.pages) ? this.page : page
      this.fetch()
    },
    searchData () {
      this.page = 1
      this.fetch()
    },
    showModal (id) {
      this.fetchDetail(id)
    },
    async fetchDetail (id) {
      const result = await axios.get(`https://rickandmortyapi.com/api/character/${id}/`)
      this.characterDetail = result.data
      this.modal = true
    }
  }
}
</script>
