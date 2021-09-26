<template>
<v-dialog v-model="dialogRemove" max-width="320px">
    <template v-slot:activator="{ on, attrs }">
        <v-btn
          small
          color="red"
          dark
          v-bind="attrs"
          v-on="on"
        >
          Hapus
        </v-btn>
    </template>
    <v-card>
    <v-card-title class="text-h6">Anda yakin ingin menghapus blog ini?</v-card-title>
    <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" text @click="dialogRemove=false">Cancel</v-btn>
        <v-btn color="blue darken-1" text @click="dialogRemove=false , removeBlog(id)">OK</v-btn>
        <v-spacer></v-spacer>
    </v-card-actions>
    </v-card>
</v-dialog>
</template>

<script>

import { mapActions , mapGetters } from 'vuex'
export default {
    data(){
        return {
            blog: {},
            dialogRemove: '',
        }
    },
    computed: {
        ...mapGetters({
        token: 'auth/token',
        })
    },
    methods: {
      ...mapActions({
            setAlert : 'alert/set',
        }),
      removeBlog (id){
        //let { id } = this.$route.params
        const config = {
            method: "post",
            url: `http://demo-api-vue.sanbercloud.com/api/v2/blog/${this.id}`,
            params : { _method : "DELETE"},
             headers: {
                    'Authorization': 'Bearer ' + this.token,
                },
        }
        this.axios(config)
                .then(() => {
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
                        text: 'Gagal!',
                    })
                })
            console.log(id)
      },
    },
    props: ['id', 'refreshData']
};
</script>
