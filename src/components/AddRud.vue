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
          Edit
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
                ></v-text-field>
              </v-col>
              <v-col cols="12">
                <v-text-field
                  v-model="description"
                  label="Deskripsi*"
                  required
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
            @click="submit"
          >
            Submit
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
            title: '',
            description: '',
            errors: [],
            dialog: '',
        }
    },
    computed: {
        ...mapGetters({
        token: 'auth/token'
        })
    },
    methods: {
        ...mapActions({
            setAlert : 'alert/set',
            setToken : 'auth/setToken'
        }),
          validationform : function(){
            this.errors = []

            if (this.title.length == 0){
                this.errors.push ('Judul tidak boleh kosong')
                this.$refs.title.focus()
            }
            if (this.description.length ==0){
                this.errors.push ('Deskripsi tidak boleh kosong')
                this.$refs.description.focus()
            }
          },

          clearform : function(){
            this.title = ''
            this.description = ''
          },
          submit(){
           this.validationform()
            let formData = new FormData()
            if(this.errors.length === 0){
                formData.append('title', this.title)
                formData.append('description', this.description)
            }
            const config = {
                method: "post",
                url: `http://demo-api-vue.sanbercloud.com/api/v2/blog`,
                headers: {
                    'Authorization': 'Bearer ' + this.token,
                    'Content-Type': 'multipart/form-data'
                },
                data: this.formData
            };

            this.axios(config)
                .then(() => {
                    this.clearform
                    this.setAlert({
                        status: true,
                        color: 'success',
                        text: 'Berhasil',
                    })
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
    props: ['justify']
};
</script>