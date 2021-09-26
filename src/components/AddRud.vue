<template>
    <v-dialog
      v-model="dialog"
      persistent
      max-width="600px"
    >
      <template v-slot:activator="{ on, attrs }">
        <v-btn
          small
          color="warning"
          dark
          v-bind="attrs"
          v-on="on"
        >
          <v-icon dark>mdi-pencil</v-icon>
        </v-btn>
      </template>
      <v-card>
       <v-form>
        <v-card-text>
              <v-col cols="12">
                <v-text-field
                  v-model="title"
                  label="Judul*"
                  required
                  :rules="titleRules"
                ></v-text-field>
              </v-col>
              <v-col cols="12">
                <v-text-field
                  v-model="description"
                  label="Deskripsi*"
                  required
                  :rules="descRules"
                ></v-text-field>
              </v-col>
          <small>*Wajib diisi</small>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="blue darken-1"
            text
            @click="dialog=false"
          >
            Cancel
          </v-btn>
          <v-btn
            color="blue darken-1"
            text
            @click="update(blog.id)"
          >
            Update
          </v-btn>
        </v-card-actions>
       </v-form>
      </v-card>
    </v-dialog>
</template>

<script>
import { mapActions, mapGetters } from 'vuex'
export default {
    data() {
        return {
            title: this.blog.title,
            description: this.blog.description,
            dialog: '',
            titleRules: [
              v => v != '' || 'Judul harus diisi'
            ],
            descRules: [
              v => v != '' || 'Deskripsi harus diisi'
            ],
        }
    },
    computed: {
        ...mapGetters({
          token: 'auth/token'
        })
    },
    methods: {
        ...mapActions({
          setAlert : 'alert/set'
        }),
        clearform : function(){
          this.title = ''
          this.description = ''
        },
        update(id){
          let formData = new FormData()
          formData.append('title', this.title)
          formData.append('description', this.description)
          const config = {
              method: "post",
              url: `https://demo-api-vue.sanbercloud.com/api/v2/blog/${id}?_method=PUT`,
              headers: {
                  'Authorization': 'Bearer ' + this.token,
                  'Content-Type': 'multipart/form-data'
              },
              data: formData
          };

          this.axios(config)
              .then(() => {
                  this.clearform()
                  this.dialog = false
                  this.setAlert({
                      status: true,
                      color: 'success',
                      text: 'Berhasil',
                  })
                  this.refreshData()
              })
              .catch(() => {
                  this.setAlert({
                      status: true,
                      color: 'error',
                      text: 'Gagal',
                  })
              })
        }
    },
    props: ['blog', 'refreshData']
};
</script>