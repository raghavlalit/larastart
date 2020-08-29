<template>
    <div class="container">
        <div class="row mt-5">
        <div class="col-md-12">
          <div class="card">
            <div class="card-header">
              <h3 class="card-title">Users Table</h3>

              <div class="card-tools">
                <a class="btn btn-success btn-sm" data-toggle="modal" data-target="#add_new" href="">Add New <i class="fas fa-user-plus"></i> </a>
              </div>
            </div>
            <!-- /.card-header -->
            <div class="card-body table-responsive no-padding">
              <table class="table table-hover">
                <tbody><tr>
                  <th>ID</th>
                  <th>Name</th>
                  <th>Email</th>
                  <th>Type</th>
                  <th>Date</th>
                  <th>Action</th>
                </tr>
                
                <tr v-for="user in users" :key="user.id">
                  <td>{{user.id}}</td>
                  <td>{{user.name}}</td>
                  <td>{{user.email}}</td>
                  <td>{{user.type |upText}}</td>
                  <td>{{user.created_at | mydate}}</td>
                  <td>
                    <a class="btn btn-info btn-sm" href=""><i class="fas fa-pencil-alt"></i> Edit</a>
                    <a class="btn btn-danger btn-sm" href=""><i class="fas fa-trash"></i> Delete</a>
                  </td>
                </tr>
                
              </tbody></table>
            </div>
            <!-- /.card-body -->
          </div>
          <!-- /.card -->
        </div>
      </div>
      <!-- Modal -->
        <div class="modal fade" id="add_new" tabindex="-1" role="dialog" aria-labelledby="add_newLabel" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered" role="document">
                <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="add_newLabel">Add New User</h5>
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                    <span aria-hidden="true">&times;</span>
                    </button>
                </div>
                <form @submit.prevent="createUser">
                    <div class="modal-body">
                        <div class="form-group">
                            <input v-model="form.name" type="text" name="name" placeholder="Enter Name"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('name') }">
                            <has-error :form="form" field="name"></has-error>
                        </div>
                        <div class="form-group">
                            <input v-model="form.email" type="email" name="email" placeholder="Email"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('email') }">
                            <has-error :form="form" field="email"></has-error>
                        </div>
                        <div class="form-group">
                            <textarea v-model="form.bio" type="text" id="bio" placeholder="Short Bio"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('bio') }"></textarea>
                            <has-error :form="form" field="bio"></has-error>
                        </div>
                        <div class="form-group">
                            <select v-model="form.type" type="text" id="type" 
                                class="form-control" :class="{ 'is-invalid': form.errors.has('type') }">
                                <option value="">Select User Type</option>
                                <option value="admin">Admin</option>
                                <option value="user">Standard User</option>
                                <option value="author">Author</option>
                            </select>
                            <has-error :form="form" field="type"></has-error>
                        </div>
                        <div class="form-group">
                            <input v-model="form.password" type="password" id="password" placeholder="Password"
                                class="form-control" :class="{ 'is-invalid': form.errors.has('password') }">
                            <has-error :form="form" field="password"></has-error>
                        </div>

                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-danger" data-dismiss="modal">Close</button>
                        <button type="submit" class="btn btn-primary">Create</button>
                    </div>
                </form>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {

        data () {
            return {
                users: {},
                // Create a new form instance
                form: new Form({
                    name:'',
                    email:'',
                    password:'',
                    type:'',
                    bio:'',
                    photo:'',
                })
            }
        },
        methods:{
            loadUsers(){
                axios.get("api/user").then(({data}) => (this.users=data.data));
            },
            createUser(){
                this.$Progress.start();
                this.form.post('api/user');
                Fire.$emit('AfterCreate');
                $('#add_new').modal('hide');
                toast.fire({
                    icon: 'success',
                    title: 'User Created successfully'
                })
                this.$Progress.finish();
            }
        },
        created() {
            this.loadUsers();
            Fire.$on('AfterCreate', ()=>{
                this.loadUsers();
            })
        }
    }
</script>
