<template>
  <div class="users">
    <div class="loading" v-if="loading">
      Loading...
    </div>
    <div v-else>

      <button @click="clear_create_button" type="button" class="btn btn-primary"
              data-toggle="modal"
              data-target="#modal-create-user">
        Create
      </button>

      <table class="table">
        <thead class="thead-dark">
          <tr>
            <th>#</th>
            <th>Agent ID</th>
            <th>Username</th>
            <th>Name</th>
            <th>Edit</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(user, index) in users" :key="user.id" class="hover">
              <td>{{ index + 1 }}</td>
              <td>{{ user.agentId }}</td>
              <td>{{ user.username }}</td>
              <td>{{ user.firstName}} {{ user.lastName}} </td>
              <td>
                <button type="button" class="btn btn-default btn-sm"
                        @click="show_user(user)"
                        data-toggle="modal"
                        data-target="#modal-update-user">
                  <span class="glyphicon glyphicon-pencil"></span> Edit
                </button>
              </td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="modal modal-default fade" id="modal-update-user" style="display: none;">
      <div class="modal-dialog">
        <div class="modal-content">

          <div class="modal-header">
            <h4 class="modal-title">Edit User</h4>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">×</span></button>
          </div> <!-- End class modal-header -->

          <div class="modal-body">

            <form v-if="selected_user != null">

              <div v-if="selected_user.agentId !== null" class="form-group">
                <label for="inputAgent">Agent ID:</label>
                <input type="text" class="form-control" id="inputAgent" aria-describedby="emailHelp"
                       :placeholder="selected_user.agentId" disabled>
                <small id="inputAgentHelp" class="form-text text-muted">Agent ID can't be changed.</small>
              </div>

              <div v-if="selected_user.username !== null" class="form-group">
                <label for="inputUsername">Username:</label>
                <input v-model="update_user.username" class="form-control" id="inputUsername" aria-describedby="emailHelp">
              </div>

              <div v-if="selected_user.firstName !== null" class="form-group">
                <label for="inputFirstName">First Name:</label>
                <input v-model="update_user.firstName" class="form-control" id="inputFirstName" aria-describedby="emailHelp">
              </div>

              <div v-if="selected_user.lastName !== null" class="form-group">
                <label for="inputLastName">Last Name:</label>
                <input v-model="update_user.lastName" class="form-control" id="inputLastName" aria-describedby="emailHelp">
              </div>

              <div v-if="selected_user.address !== null" class="form-group">
                <label for="inputAddress">Address:</label>
                <input v-model="update_user.address" class="form-control" id="inputAddress" aria-describedby="emailHelp">
              </div>

              <button @click="send_user_delete" type="delete" class="btn btn-danger">Delete</button>

            </form>

          </div>  <!-- End class modal-body -->
          <div class="modal-footer">
            <button type="button" class="btn btn-outline pull-left sb-btn" data-dismiss="modal">Cancel</button>
            <button @click="send_user_update" type="button" class="btn btn-primary">Update
            </button>
          </div>
        </div>  <!-- /.modal-content -->
      </div>      <!-- /.modal-dialog -->
    </div>

    <div class="modal modal-default fade" id="modal-create-user" style="display: none;">
      <div class="modal-dialog">
        <div class="modal-content">

          <div class="modal-header">
            <h4 class="modal-title">Create User</h4>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">×</span></button>
          </div> <!-- End class modal-header -->

          <div class="modal-body">

            <form>

              <div  class="form-group">
                <label for="inputCreateUsername">Username:</label>
                <input v-model="create_user.username" class="form-control" id="inputCreateUsername" aria-describedby="emailHelp">
              </div>

              <div class="form-group">
                <label for="inputCreateFirstName">First Name:</label>
                <input v-model="create_user.firstName" class="form-control" id="inputCreateFirstName" aria-describedby="emailHelp">
              </div>

              <div  class="form-group">
                <label for="inputCreateLastName">Last Name:</label>
                <input v-model="create_user.lastName" class="form-control" id="inputCreateLastName" aria-describedby="emailHelp">
              </div>

              <div class="form-group">
                <label for="inputCreateAddress">Address:</label>
                <input v-model="create_user.address" class="form-control" id="inputCreateAddress" aria-describedby="emailHelp">
              </div>

            </form>

          </div>  <!-- End class modal-body -->
          <div class="modal-footer">
            <button type="button" class="btn btn-outline pull-left sb-btn" data-dismiss="modal">Cancel</button>
            <button @click="send_user_create" type="button" class="btn btn-primary">Create
            </button>
          </div>
        </div>  <!-- /.modal-content -->
      </div>      <!-- /.modal-dialog -->
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
        selected_user: null,
        update_user: {
          username: null,
          firstName: null,
          lastName: null,
          address: null
        },
        create_user: {
          username: null,
          firstName: null,
          lastName: null,
          address: null
        },
        error_message: "",
        users: []
      }
    },
    created () {
      // fetch the data when the view is created and the data is
      // already being observed
      this.fetchData()
    },
    methods: {
      fetchData () {
        this.post = null;
        this.loading = true;
        this.index_count = 1;


        const options = {
          uri: conf.API_SERVER_HOST + '/users',
          method: 'GET'
        };

        // first get the data from their demo site
        rp(options)
          .then(function (result) {
            this.loading = false;
            // returns a json format
//            console.log(result);
            this.users = JSON.parse(result);

            console.log(this.users.length);

            this.users.sort(function (a, b) {
              if (a.agentId > b.agentId) {
                return 1;
              } else if (a.agentId < b.agentId) {
                return -1;
              } else {
                return 0;
              }
            });


          }.bind(this))
          .catch(function (err) {
            this.loading = false;
            // something failed
            console.log(err);
          }.bind(this));


      },
      show_user: function (user) {
        this.selected_user = user;
        this.update_user.username = user.username;
        this.update_user.firstName = user.firstName;
        this.update_user.lastName = user.lastName;
        this.update_user.address = user.address;
      },
      send_user_update: function () {
        console.log("Updating this user: " +  this.selected_user.agentId);
        console.log(this.update_user);

        const options = {
          uri: conf.API_SERVER_HOST + '/users/' + this.selected_user.agentId,
          method: 'PUT',
          body: this.update_user,
          json: true
        };

        rp(options)
          .then(function (result) {

            console.log("Result:");
            console.log(result);
            location.reload(); // refresh page

          }.bind(this))
          .catch(function (err) {
            // something failed
            console.log("Error:");
            console.log(err);
          }.bind(this));

      },
      clear_create_button: function () {
        this.create_user =  {
          username: null,
          firstName: null,
          lastName: null,
          address: null
        };
      },
      send_user_delete: function () {
        console.log("Deleting this user: " + this.selected_user.agentId);

        const options = {
          uri: conf.API_SERVER_HOST + '/users/' + this.selected_user.agentId,
          method: 'DELETE'
        };


        rp(options)
          .then(function (result) {

            console.log("Result:");
            console.log(result);
            location.reload(); // refresh page

          }.bind(this))
          .catch(function (err) {
            // something failed
            console.log("Error:");
            console.log(err);
          }.bind(this));
      },
      send_user_create: function () {
        console.log("Creating user");
        console.log(this.create_user);

        const options = {
          uri: conf.API_SERVER_HOST + '/users',
          method: 'POST',
          body: this.create_user,
          json: true
        };

        rp(options)
          .then(function (result) {

            console.log("Result:");
            console.log(result);
            location.reload(); // refresh page

          }.bind(this))
          .catch(function (err) {
            // something failed
            console.log("Error:");
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
