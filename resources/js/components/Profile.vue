<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-12 mt-3">
                <div class="card card-widget widget-user">
              <!-- Add the bg color to the header using any of the bg-* classes -->
              <div class="widget-user-header text-white" style="background-image:url('./img/jules.jpg');">
                <h3 class="widget-user-username text-right">{{ form.name }}</h3>
                <h5 class="widget-user-desc text-right">{{ form.type }}</h5>
              </div>
              <div class="widget-user-image">
                <img class="img-circle" :src="getProfilePhoto()" alt="User Avatar">
              </div>
              <div class="card-footer">
                <div class="row">
                  <div class="col-sm-4 border-right">
                    <div class="description-block">
                      <h5 class="description-header">3,200</h5>
                      <span class="description-text">SALES</span>
                    </div>
                    <!-- /.description-block -->
                  </div>
                  <!-- /.col -->
                  <div class="col-sm-4 border-right">
                    <div class="description-block">
                      <h5 class="description-header">13,000</h5>
                      <span class="description-text">FOLLOWERS</span>
                    </div>
                    <!-- /.description-block -->
                  </div>
                  <!-- /.col -->
                  <div class="col-sm-4">
                    <div class="description-block">
                      <h5 class="description-header">35</h5>
                      <span class="description-text">PRODUCTS</span>
                    </div>
                    <!-- /.description-block -->
                  </div>
                  <!-- /.col -->
                </div>
                <!-- /.row -->
              </div>
            </div>

            <div class="card">
              <div class="card-header p-2">
                <ul class="nav nav-pills">
                  <li class="nav-item"><a class="nav-link active" href="#activity" data-toggle="tab">Activity</a></li>
                  <li class="nav-item"><a class="nav-link" href="#settings" data-toggle="tab">Settings</a></li>
                </ul>
              </div><!-- /.card-header -->
              <div class="card-body">
                <div class="tab-content">
                  <div class="tab-pane active" id="activity">
                      <h3>Display User Activity</h3>
                  </div>
                  <!-- /.tab-pane -->

                  <div class="tab-pane" id="settings">
                    <form class="form-horizontal">
                      <div class="form-group row">
                        <label for="inputName" class="col-sm-2 col-form-label">Name</label>
                        <div class="col-sm-10">
                          <input type="text" v-model="form.name" class="form-control" id="inputName" placeholder="Name" :class="{ 'is-invalid': form.errors.has('name') }">
                          <has-error :form="form" field="name"></has-error>
                        </div>
                      </div>
                      <div class="form-group row">
                        <label for="inputEmail" class="col-sm-2 col-form-label">Email</label>
                        <div class="col-sm-10">
                          <input type="email" v-model="form.email" class="form-control" id="inputEmail" placeholder="Email" :class="{ 'is-invalid': form.errors.has('email') }">
                          <has-error :form="form" field="email"></has-error>
                        </div>
                      </div>
                      <div class="form-group row">
                        <label for="inputExperience" class="col-sm-2 col-form-label">Experience</label>
                        <div class="col-sm-10">
                          <textarea class="form-control" id="inputExperience" placeholder="Experience"></textarea>
                        </div>
                      </div>
                      <div class="form-group row">
                        <label for="photo" class="col-sm-2 col-form-label">Profile Photo</label>
                        <div class="col-sm-10">
                          <input type="file" @change="updateProfile" name="photo" class="form-input">
                        </div>
                      </div>
                      <div class="form-group row">
                        <label for="passport" class="col-sm-2 col-form-label">Password</label>
                        <div class="col-sm-10">
                          <input type="password" v-model="form.password" id="password" name="password" class="form-control" placeholder="Change Password" :class="{ 'is-invalid': form.errors.has('email') }">
                          <has-error :form="form" field="password"></has-error>
                        </div>
                      </div>

                      <div class="form-group row">
                        <div class="offset-sm-2 col-sm-10">
                          <button @click.prevent="updateInfo" type="submit" class="btn btn-success">Update</button>
                        </div>
                      </div>
                    </form>
                  </div>
                  <!-- /.tab-pane -->
                </div>
                <!-- /.tab-content -->
              </div><!-- /.card-body -->
            </div>
            </div>
        </div>
    </div>
</template>

<style>
.widget-user-header{
    background-position: center center;
    background-size: cover;
}
</style>

<script>
    export default {
        data(){
            return {
                form: new Form({
                    id: '',
                    name: '',
                    email: '',
                    password: '',
                    type: '',
                    bio: '',
                    photo: ''
            })

            }
        },
        methods: {
            getProfilePhoto(){
                let prefix = (this.form.photo.match(/\//) ? '' : '/img/profile/');
                return prefix + this.form.photo;
            },
            updateInfo() {
                this.$Progress.start();
                if(this.form.password == ""){
                this.form.password = undefined;
            }
                this.form.put('api/profile/')
                .then(() => {

                    this.$Progress.finish();
                })
                .catch(() => {

                    this.$Progress.fail();
                })
            },
            updateProfile(e){
                //console.log('uploading');
                let file =  e.target.files[0];
                console.log(file);
                let reader = new FileReader();

                if(file['size'] < 2111775){
                reader.onloadend = (file) => {
                   // console.log('RESULT', reader.result)
                   this.form.photo = reader.result;
                }
                reader.readAsDataURL(file);
                } else{
                    Swal.fire({
                        title : 'Oops...',
                        icon: 'error',
                        text: 'The file is too large to upload.'
                    })
                }
            }
        },

        mounted() {
            console.log('Component mounted.')
        },

        created() {
            axios.get("api/profile")
            .then(({ data }) => (this.form.fill(data)));
        }
    }
</script>
