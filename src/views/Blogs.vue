<template>
  <v-container class="ma-0 pa-0" grid-list-sm>
  <v-subheader>
    All Blogs
  </v-subheader>
    <v-row justify="center">
        <add-blog v-if="this.token!==''" :refreshData="go"/>
    </v-row>
    <br>
    <v-layout wrap>
      <blog-item-component 
        v-for="blog in blogs"  
        :key="`blog-${blog.id}`"
        :blog="blog"
        :refreshData="go"
      ></blog-item-component>
    </v-layout>
    <v-pagination
        v-model="page"
        @input="go"
        :length="lengthPage"
        :total-visible="6"
    ></v-pagination>
  </v-container>
</template>

<script>
import { mapGetters } from 'vuex';
import AddBlog from '../components/AddBlog.vue';
import BlogItemComponent from '../components/BlogItemComponent.vue';
export default {
    data: () => ({
        apiDomain: "https://demo-api-vue.sanbercloud.com",
        blogs: [],
        page: 0,
        lengthPage: 0,
        perPage: 0,
    }),
    components: {
        'add-blog' : AddBlog ,
        'blog-item-component': BlogItemComponent
    },
    computed: {
        ...mapGetters({
        token: 'auth/token'
        })
    },
    methods: {
        go(){
            const config = {
                method: "get",
                url: `${this.apiDomain}/api/v2/blog?page=${this.page}`
            };

            this.axios(config)
                .then(response => {
                    let { blogs } = response.data
                    this.blogs = blogs.data
                    this.page = blogs.current_page
                    this.lengthPage = blogs.last_page
                    this.perPage = blogs.per_page
                })
                .catch(error => {
                    console.log(error);
                })
        }
    },
    created(){
        this.go()
    }
};
</script>

