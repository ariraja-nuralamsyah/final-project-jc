<template>
  <v-app>
    <Alert/>
    <Dialog/>
    <v-app-bar 
      app 
      color="deep-purple accent-4"
      dense
      dark>

        <v-toolbar-title style="width:100px">Team 3</v-toolbar-title>
        <v-tabs align-with-title>
          <v-tab
            v-for="(item, index) in menus"
            :key="`menu-${index}`"
            :to="item.route">
            {{ item.title }}</v-tab>
        </v-tabs>
      

      <!-- pemisah konten -->
      <v-spacer></v-spacer>
      
      <v-list-item-avatar v-if="!guest">
        <v-img :src="user.photo_profile ? apiDomain + user.photo_profile : 'https://randomuser.me/api/portraits/men/74.jpg'"></v-img>
      </v-list-item-avatar>  

      <v-btn color="primary" @click="login" class="mr-3" v-if="guest">
        <v-icon left>mdi-lock</v-icon>
        Login
      </v-btn>
      <v-btn color="success" @click="register" v-if="guest">
        <v-icon left>mdi-account</v-icon>
        Register
      </v-btn>

      <div class="pa-2" v-if="!guest">
        <v-btn block color="red" dark @click="logout">
          <div v-if="onLoad == true"><v-progress-circular
              indeterminate
              color="black"
              :width="1"
              :size="15"
          ></v-progress-circular>
          </div>
          <div v-else>
              <v-icon right dark>mdi-lock-open</v-icon>
          </div>
          &nbsp; Logout
        </v-btn>
      </div>
       
   
    </v-app-bar>

    <!-- Sizes your content based upon application components -->
    <v-main>
      <v-container>
        <v-slide-y-transition>
          <router-view></router-view>
        </v-slide-y-transition>
      </v-container>
    </v-main>

    <v-footer>
      <v-col
        class="text-center"
        cols="12"
      >
        @Team3 | VueJS 
      </v-col>
    </v-footer>
  </v-app>
</template>

<script>
import { mapActions, mapGetters } from 'vuex';

export default {
  name: 'App',
  components: { 
    Alert : () => import('./components/Alert.vue'),
    Dialog : () => import('./components/Dialog.vue')
  },
  data: () => ({
    drawer: false,
    menus: [
      {title: 'Home', icon: 'mdi-home', route: '/'},
      {title: 'Blogs', icon: 'mdi-note', route: '/blogs'},
    ],
    apiDomain: "https://demo-api-vue.sanbercloud.com",
    onLoad: false,
  }),
  computed: {
    ...mapGetters({
      guest: 'auth/guest',
      user: 'auth/user',
      token: 'auth/token'
    })
  },
  methods: {
    logout(){
      this.onLoad = true
      let config = {
        method: 'post',
        url: `${this.apiDomain}/api/v2/auth/logout`,
        headers: {
          'Authorization': 'Bearer ' + this.token,
        },
      };

      this.axios(config)
        .then(() => {
          this.setToken('')
          this.setUser({})

          this.setAlert({
            status: true,
            color: 'success',
            text: 'Anda berhasil logout',
          })
          this.onLoad = false
        })
        .catch(() => {
          this.setAlert({
            status: true,
            color: 'error',
            text: 'Anda gagal logout',
          })
        })
    },
    login(){
      this.setDialogComponent({'component' : 'login'})
    },
    register(){
      this.setDialogComponent({'component' : 'register'})
    },
    ...mapActions({
      setAlert : 'alert/set',
      setDialogComponent : 'dialog/setComponent', 
      setToken : 'auth/setToken',
      checkToken : 'auth/checkToken',
      setUser : 'auth/setUser',
    })
  },
  mounted(){
    if(this.token){
      this.checkToken(this.token)
    }
  }
};
</script>
