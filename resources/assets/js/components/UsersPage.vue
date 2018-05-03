<template>
  <div class="row">
    <div class="col-lg-4">
      <form class="form-horizontal">
        <div class="form-group">
          <label for="uuid" class="col-sm-2 control-label">ID</label>
          <div class="col-sm-10">
            <input type="text" class="form-control" id="uuid" readonly v-model="form.uuid">
          </div>
        </div>
        <div class="form-group">
          <label for="name" class="col-sm-2 control-label">Nama</label>
          <div class="col-sm-10">
            <input type="text" class="form-control" id="name" placeholder="Masukan nama" v-model="form.name">
          </div>
        </div>
        <div class="form-group">
          <label for="address" class="col-sm-2 control-label">Address</label>
          <div class="col-sm-10">
            <input type="text" class="form-control" id="address" placeholder="Masukan alamat" v-model="form.address">
          </div>
        </div>
        <div class="form-group">
          <div class="col-sm-offset-2 col-sm-10">
            <button type="button" class="btn btn-primary btn-block" v-on:click="store">Submit</button>
          </div>
        </div>
      </form>
    </div>
    <div class="col-lg-8">
      <div class="alert alert-danger" role="alert" v-show="errorMessage" v-html="errorMessage"></div>

      <table class="table"> 
        <thead> 
          <tr>
            <th width="20">User ID</th> 
            <th>Nama</th> 
            <th>Alamat</th> 
          </tr> 
        </thead> 
        <tbody> 
          <tr v-for="user in users"> 
            <td>{{ user.uuid }}</td> 
            <td>{{ user.name }}</td> 
            <td>{{ user.address }}</td>
          </tr>
        </tbody> 
      </table>
    </div>
  </div>
</template>

<script>
export default {
  name: 'user-page',
  data() {
    return {
      form: {},
      users: [],
      errorMessage: ''
    }
  },
  methods: {
    store() {
      const that = this

      axios.post('/api/users', this.form).then(res => {
        that.users.push(res.data)

        that.form = {}
        that.refreshUuid()
      }).catch(err => {
        if (err.response.status == 422) {
          var errorMessage = ''

          if (err.response.data.errors.address)
            errorMessage += err.response.data.errors.address[0] + '<br>';

          if (err.response.data.errors.name)
            errorMessage += err.response.data.errors.name[0] + '<br>';
          
          that.errorMessage = errorMessage
        }
      })
    },
    refreshUuid() {
      this.form.uuid = Math.random().toString(36).substring(7)
    }
  },
  mounted() {
    const that = this

    this.refreshUuid()

    axios.get('/api/users').then(res => {
      that.users = res.data
    })
  }
}
</script>
