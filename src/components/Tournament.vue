<template>
    <div>
        <b>
            <router-link to="/">Go to Home</router-link>
        </b>

        <div>
            <button @click="add()">ADD</button>
            <button @click="clear()">Clear</button>
        </div>

        <div class="api-list">
            <ul class="api-list__item" v-for="(api, api_index) in apis">
                <label for="">{{api_index}}</label>
                <select v-model="api.method" name="method">
                    <option v-for="method in methods" v-bind:value="api_index">
                        {{ method }}
                    </option>
                </select>
                <div v-for="(parent, parent_index) in api.parents">
                    <select v-model="parent.key" name="parent">
                        <option></option>
                        <option v-for="(api, api_index_child) in apis" v-if="api_index != api_index_child" v-bind:value="api_index_child">
                            {{ api_index_child }}
                        </option>
                    </select>
                    <button @click="removeParent(api_index, parent_index)" v-if="api.parents.length != 1">X</button>
                    <button @click="addParent(api_index)">➕</button>
                </div>
                <input v-model="api.url">
                <button @click="remove(api_index)">X</button>
                <div class="api-list__item" v-for="(param, param_index) in api.params">
                    KEY:<input type="text" v-model="param.key">
                    VALUE:<input type="text" v-model="param.value">
                    <button @click="removeParams(api_index, param_index)">✖</button>
                </div>
                <button @click="addParams(api_index)">➕</button>
                <button @click="doRequest(api_index)">📩</button>
                <hr v-if="apis.length-1 != api_index">
            </ul>
            <hr>
        </div>
    </div>
</template>


<script>
  import Vue from 'vue';
  import Vuex from 'vuex';
  import Axios from 'axios'; // api lib
  import createPersistedState from 'vuex-persistedstate'

  Vue.use(Vuex);

  const store = new Vuex.Store({
    state: {
      apis: [
        {
          method: 'GET', // todo
          url : 'https://jsonplaceholder.typicode.com/posts/1',
          params : [
            {key: '', value: ''},
          ],
          parents: [
            {key: null}
          ],
          done: false
        }
      ]
    },
    plugins: [createPersistedState()],
    mutations: {
      clear: state => state.apis = [],
      addParam: (state, index) => state.apis[index].params.push({key: '', value: ''}),
      removeParam: (state, arg) => Vue.delete(state.apis[arg.index].params, arg.paramIndex),
      allReplace: (state, apis) => state.apis = apis
    }
  });

  export default {
    name: 'tournament',
    computed: {
      apis() {
        return store.state.apis;
      },
      methods() {
        return ['GET', 'POST', 'PUT', 'DELETE', 'PATCH']
      }
    } ,
    watch: {
      apis: {
        handler(){
          store.commit('allReplace', this.apis)
        },
        deep: true
      }
    },
    methods: {
      add: function(event) {
        this.apis.push(
          {
            method: 'GET', // todo
            url : '',
            params : [
              {key: '', value: ''},
            ],
            parents: [
              {key: null}
            ],
            done: false
          }
        );
      },
      clear: function () {
        store.commit('clear');
      },
      remove: function (key) {
        Vue.delete(this.apis, key);
      },
      addParent: function (index) {
        this.apis[index].parents.push({parent: null});
      },
      removeParent: function (index, parent_index) {
        Vue.delete(this.apis[index].parents, parent_index);
      },
      addParams: function (index) {
        store.commit('addParam', index);
      },
      removeParams: function (index, paramsIndex) {
        store.commit('removeParam', {
          index: index,
          paramIndex: paramsIndex
        });
      },
      doRequest: function (api_index) {
        console.log('doRequest');
        var self = this;
        var api = this.apis[api_index];
        Axios.get(api.url, api.params)
          .then(function (result) {
            console.dir(result);
            self.callChild(api_index, result);
          })
      },
      callChild: function (parent, result) {
        var self = this;
        this.apis.map(function (obj, index) {
          if (obj.parent !== parent) {
            return false; //skip
          }

          console.log('callChild');

          var api = self.apis[index];
          Axios.get(api.url, api.params)
            .then(function (result) {
              console.log(result);
              self.callChild(index, result);
            })
        });
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>

</style>