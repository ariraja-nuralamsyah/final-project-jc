<template>
    <v-flex xs3>
      
        <v-card>
        <router-link
          :to="`/blog/${blog.id}`"
          style="text-decoration: none; color: inherit;"
        >
          <v-img
            :src="blog.photo ? apiDomain + blog.photo : 'https://picsum.photos/200/300'"
            class="white--text"
            height="200px"
          >
            <v-card-title
              class="fill-height align-end"
              v-text="blog.title"
            >
            </v-card-title>
          </v-img>
        </router-link>
          
          <v-card-actions>
            <v-progress-linear
              color="blue-grey"
              height="7"
            ></v-progress-linear>
          </v-card-actions>


          <p style="padding: 8px">{{ blog.title.substring(0,15) }}...</p>

          <v-container class="d-flex flew-row-reverse justify-end">
            <add-rud/>
            <upload-form :id="blog.id"/>
          </v-container>
          
        </v-card>
        
    </v-flex>
</template>

<script>
import AddRud from '../components/AddRud.vue'
import UploadImage from '../components/UploadImage.vue'
export default{
    data: () => ({
        apiDomain: 'https://demo-api-vue.sanbercloud.com',
    }),
    props: ['blog'] ,
    components : {
      'add-rud' : AddRud,
      'upload-form' : UploadImage,
    }
}
</script>