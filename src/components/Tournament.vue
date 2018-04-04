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
                <select v-model="api.parent" name="parent">
                    <option v-for="(api, api_index_child) in apis" v-if="api_index != api_index_child" v-bind:value="api_index_child">
                        {{ api_index_child }}
                    </option>
                </select>
                <label for="">{{api_index}}</label>
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
        var self = this;
        var api = this.apis[api_index];
        Axios.get(api.url, api.params)
          .then(function (result) {
            self.callChild(api_index, result);
          })
      },
      callChild: function (parent, result) {
        var self = this;
        this.apis.map(function (obj, index) {
          if (obj.parent !== parent) {
            return false; //skip
          }

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