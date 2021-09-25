<template>
    <v-dialog
      v-model="dialog"
      persistent
      max-width="600px"
    >
      <template v-slot:activator="{ on, attrs }">
        <v-btn
          small
          color="blue"
          dark
          v-bind="attrs"
          v-on="on"
        >
          Upload
        </v-btn>
      </template>
      <v-card>
       <v-form>
        <v-card-text>
          <v-card-title class="headline">Upload Image</v-card-title>
        </v-card-text>
        <v-file-input
            label="Choose image"
            ref="myFile"
            show-size
            accept=".jpg"
            outlined
            dense
            prepend-icon="mdi-image"
            @change="onFileChange"
            style="padding: 0px 24px"
          ></v-file-input>
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
            Upload
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
            file: '',
            errors: [],
            dialog: false,
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
          onFileChange : function(file){
            this.errors = []

            console.log(this.id)
            this.file = file

            if (this.file == undefined || this.file == '') {
              this.errors.push('File tidak ditemukan')
              this.$refs.myFile.focus()
            }
          },
          clearform : function(){
            this.file = ''
          },
          validationform() {
            this.errors = []

            if (this.file == undefined || this.file == '') {
              this.errors.push('File tidak ditemukan')
              this.$refs.myFile.focus()
            }
          },
          submit(){
           this.validationform()
            let formData = new FormData()
            if(this.errors.length === 0){
                formData.append('photo', this.file)

                const config = {
                    method: "post",
                    url: `http://demo-api-vue.sanbercloud.com/api/v2/blog/${this.id}/upload-photo`,
                    headers: {
                        'Authorization': 'Bearer ' + this.token,
                        'Content-Type': 'multipart/form-data'
                    },
                    data: this.formData
                };
                console.log(this.file)
                this.axios(config)
                    .then(() => {
                        this.clearform
                        this.dialog = false
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
            
          }
    },
    props: ['id']
};
</script>