<template>
  <div class="users">
    <div class="loading" v-if="loading">
      Loading...
    </div>
    <div v-else>
      <table class="table">
        <thead class="thead-dark">
          <tr>
            <th>Agent ID</th>
            <th>Username</th>
            <th>Name</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="user in users" :key="user.id" class="hover">
            <th scope="row">1</th>
            <td>Mark</td>
            <td>Otto</td>
          </tr>
        </tbody>
      </table>
    </div>

  </div>
</template>

<script>
  import axios from 'axios';
  import rp from 'request-promise';
  import conf from '../conf/conf';

export default {
    data () {
      return {
        loading: false,
        post: null,
        error: null,
        users: []
      }
    },
    created () {
      // fetch the data when the view is created and the data is
      // already being observed
      this.fetchData()
    },
//    watch: {
//      // call again the method if the route changes
//      '$route': 'fetchData'
//    },
    methods: {
      fetchData () {
        this.post = null;
        this.loading = true;


        const options = {
          uri: conf.API_SERVER_HOST + '/',
          method: 'GET'
        };

        // first get the data from their demo site
        rp(options)
          .then(function (result) {
            this.loading = false;
            // returns a json format
            console.log(result);
            this.users = result;
          }.bind(this))
          .catch(function (err) {
            this.loading = false;
            // something failed
            console.log(err);
          }.bind(this));


      }
    }
  }
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
</style>
