<template>
  <v-app>
    <Alert/>
    <Dialog/>
    <v-app-bar 
      app 
      color="deep-purple accent-4"
      dense
      dark>

        <v-toolbar-title style="width:100px">WeSite</v-toolbar-title>
        <v-tabs align-with-title>
          <v-tab
            v-for="(item, index) in menus"
            :key="`menu-${index}`"
            :to="item.route">
            {{ item.title }}</v-tab>
        </v-tabs>
      

      <!-- pemisah konten -->
      <v-spacer></v-spacer>
      
      <v-menu
        offset-y
      >
        <template v-slot:activator="{ on, attrs }">
          <v-btn
            icon
            v-bind="attrs"
            v-on="on"
          >
            <v-icon>mdi-account</v-icon>
          </v-btn>
        </template>

        <v-card v-if="!guest">
          <v-list>
            <v-list-item>
              <v-list-item-avatar>
                <v-img :src="user.photo_profile ? apiDomain + user.photo_profile : 'https://randomuser.me/api/portraits/men/74.jpg'"></v-img>
              </v-list-item-avatar>

              <v-list-item-content>
                <v-list-item-title>{{ user.name }}</v-list-item-title>
                <v-list-item-subtitle>{{ user.email }}</v-list-item-subtitle>
              </v-list-item-content>
            </v-list-item>
          </v-list>

          <v-divider></v-divider>
          <v-list>
            <v-list-item  @click="logout">
              <div v-if="onLoad == true"><v-progress-circular
                  indeterminate
                  color="black"
                  :width="1"
                  :size="15"
              ></v-progress-circular>
              </div>
              <div v-else>
                  <v-icon left dark color="black">mdi-logout</v-icon>
              </div>
              &nbsp; Logout
            </v-list-item>
          </v-list>
        </v-card>
        <v-card v-else-if="guest">
          <v-list>
            <v-list-item @click="login">
              <v-icon left>mdi-login</v-icon>
              Login
            </v-list-item>
            <v-list-item @click="register">
              <v-icon left>mdi-account-plus</v-icon>
              Register
            </v-list-item>
          </v-list>
        </v-card>
      </v-menu>
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
        @Team - 3 | WeSite 
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
