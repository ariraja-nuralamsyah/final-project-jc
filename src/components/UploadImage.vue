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
          <v-icon dark>mdi-tray-arrow-up</v-icon>
        </v-btn>
      </template>
      <v-card>
       <v-form>
        <v-card-text>
          <v-card-title class="headline">Upload Image</v-card-title>
        </v-card-text>
        <v-file-input
            label="Choose image"
            v-model="fileInput"
            ref="myFile"
            show-size
            accept="image/jpeg"
            outlined
            dense
            prepend-icon="mdi-image"
            @change="onFileChange"
            style="padding: 0px 24px"
            :error="errors.length > 0"
            :error-message="errors[0]"
            :rules="rules"
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
            fileInput: undefined,
            errors: [],
            dialog: false,
            invalid: false,
            rules: [
              v => (!!v && !this.invalid) || 'File is required'
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
          onFileChange : function(file){
            this.file = file
            this.invalid = false
          },
          clearform : function(){
            this.file = ''
            this.fileInput = undefined
          },
          submit(){
            let formData = new FormData()
            if(this.file != undefined && this.file != ''){
                formData.append('photo', this.file)

                const config = {
                    method: "post",
                    url: `http://demo-api-vue.sanbercloud.com/api/v2/blog/${this.id}/upload-photo`,
                    headers: {
                        'Authorization': 'Bearer ' + this.token,
                        'Content-Type': 'multipart/form-data'
                    },
                    data: formData
                };
                console.log(this.file)
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
            } else {
              this.error = []
              this.invalid = true
              this.setAlert({
                  status: true,
                  color: 'error',
                  text: 'File tidak boleh kosong',
              })
              this.error.push("File kosong")
            }
            
          }
    },
    props: ['id', 'refreshData']
};
</script>