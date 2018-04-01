<template>
    <div>
        <b>
            <router-link to="/">Go to Home</router-link>
        </b>

        <div>
            <button @click="add()">ADD</button>
        </div>

        <div class="api-list">
            <ul class="api-list__item" v-for="(api, api_index) in apis">
                <select v-model="api.parent" v-bind:name="'api_parent' + api_index">
                    <option v-for="(api, api_index) in apis" v-bind:value="api_index">
                        {{api_index}}
                    </option>
                </select>
                <label for="">{{api_index}}</label>
                <label for="">Parent</label><input v-model="api.parent">
                <button @click="remove(api_index)">X</button>
                <input v-model="api.url">
                <div class="api-list__item" v-for="(param, param_index) in api.params">
                    KEY:<input type="text" v-model="param.key">
                    VALUE:<input type="text" v-model="param.value">
                    <button @click="removeParams(api_index, param_index)">✖</button>
                </div>
                <button @click="addParams(api_index)">➕</button>
                <button @click="doRequest(api_index)">📩</button>
            </ul>
        </div>
    </div>
</template>

<script>
  import Vue from 'vue';
  import Axios from 'axios';

  export default {
    name: 'tournament',
    data: function () {
      return {
        parents: {},
//        apis: [
//          {
//            url : '',
//            params : [
//              {key: '', value: ''},
//            ],
//            parent: null,
//            done: false
//          }
//        ]
        apis: [
          {
            url : 'https://jsonplaceholder.typicode.com/posts/1',
            params : [
              {key: '', value: ''},
            ],
            parent: null,
            done: false
          }
        ]
      }
    },
    methods: {
      add: function(event) {
        this.apis.push(
          {
            url : '',
            params : [
              {key: '', value: ''},
            ],
            parent: null,
            done: false
          }
        );
      },
      remove: function (key) {
        Vue.delete(this.apis, key);
      },
      addParams: function (key) {
        this.apis[key].params.push({key: '', value: ''});
      },
      removeParams: function (key, paramsIndex) {
        Vue.delete(this.apis[key].params, paramsIndex);
      },
      doRequest: function (api_index) {
        var api = this.apis[api_index];
        Axios.get(api.url, api.params)
          .then(function (result) {
            console.log(result);
          })
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>

</style>