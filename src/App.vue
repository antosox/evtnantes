<template>
  <div id="app">

    <input type="text" v-model="search">

    <button @click="modifiyState">touch me</button>

    <table v-if="state">
      <thead>
        <tr>
          <th>Nom</th>
          <th>Description</th>
          <th>Lieu</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="record in searching">
          <td>{{record.fields.nom}}</td>
          <td>{{record.fields.description}}</td>
          <td>{{record.fields.lieu}}</td>
        </tr>
      </tbody>
    </table>

    <div v-if="!state">
      <div class="d-flex wrap">
        <div class="card" v-for="(record, key) in results" :key="key">
          <div class="nom">
            {{record.fields.nom}}
          </div>
          <div class="description">
            {{record.fields.description}}
          </div>
          <div class="lieu">
            {{record.fields.lieu}}
          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<script>

import Axios from 'axios'

export default {
  name: 'app',

  data(){
    return {
      results: null,
      search: '',
      state: true
    }
},

  methods: {

    // eslint-disable-next-line vue/return-in-computed-property
    request: function(){
      let self = this
      // eslint-disable-next-line vue/no-async-in-computed-properties
      Axios
              .get('https://data.nantesmetropole.fr/api/records/1.0/search/?dataset=244400404_agenda-evenements-nantes-nantes-metropole&facet=emetteur&facet=rubrique&facet=lieu&facet=ville')
              // eslint-disable-next-line vue/no-side-effects-in-computed-properties
              .then(response => (self.results = response.data.records, console.log(self.results)))
              // eslint-disable-next-line no-console

    },

    modifiyState: function () {
      this.state = !this.state
    }
  },

  mounted: function() {
    this.request()
  },

  computed: {
    searching(){
      var results = this.results
      var searchingString = this.search

      if(!searchingString){
        return results
      }

      searchingString = searchingString.trim().toLocaleLowerCase()

      results = results.filter(function (record) {
        if (record.fields.nom.toLowerCase().indexOf(searchingString) !== -1){
          return record
        }
      })
      return results
    }
}
}
</script>

<style>
#app {}

  .d-flex{
    display: flex;
  }

  .wrap{
    flex-wrap: wrap;
  }

  .card{
    height: 500px;
    width: 20%;
    max-width: 20%;
    min-width: 20%;
    margin: 20px;
    padding: 5px;
    border: solid 1px deeppink;
    border-radius: 10px;
    position: relative;

  }

.nom{
  font-weight: bold;
  padding: 5px;
}

  .lieu{
    font-style: italic;
    position: absolute;
    bottom: 0;
  }
</style>
