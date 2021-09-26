<template>
    <v-card>
        <v-toolbar dark color="primary">
            <v-btn icon dark @click.native="close">
                <v-icon>mdi-close</v-icon>
            </v-btn>
            <v-toolbar-title>Register</v-toolbar-title>
        </v-toolbar>
        <v-divider></v-divider>
        <v-container fluid>
            <v-form ref="form" v-model="isValid">
                <v-text-field
                    v-model="name"
                    label="Name"
                    append-icon="mdi-account"
                    :rules="nameRules"
                    required
                ></v-text-field>
                <v-text-field
                    v-model="email"
                    label="E-mail"
                    append-icon="mdi-email"
                    :rules="emailRules"
                    required
                ></v-text-field>
                <v-text-field
                    v-model="password"
                    :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off' "
                    :type="showPassword ? 'text' : 'password' "
                    label="Password"
                    counter
                    @click:append="showPassword = !showPassword"
                    :rules="passwordRules"
                ></v-text-field>
                <v-img :src="image != null ? image.length != 0 ? url : imageDummy : imageDummy" style="width:100px"></v-img>
                <v-file-input
                    accept="image/png, image/jpeg, image/bmp"
                    placeholder="Pick an photo profile"
                    prepend-icon="mdi-camera"
                    label="Photo Profile"
                    ref="photo"
                    :rules="photoRules"
                    @change="Preview_image"
                    v-model="image"
                >
                </v-file-input>

                <div class="text-xs-center">
                    <v-btn
                        color="success lighten-1"
                        @click="submit"
                        :disabled="!isValid"
                    >
                        Register &nbsp;
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
                    </v-btn>
                </div>
            </v-form>
        </v-container>
    </v-card>
</template>

<script>
import { mapActions } from 'vuex'
export default {
    data() {
        return{
            email: '',
            showPassword: false,
            password: '',
            name: '',
            image: [],
            imageDummy: 'https://cdn2.iconfinder.com/data/icons/avatar-profile/431/avatar_contact_tie_user_default_suit_display-512.png',
            url: null,
            onLoad: false,
            isValid: true,
            apiDomain: 'https://demo-api-vue.sanbercloud.com',
        }
    },
    computed: {
        nameRules() {
            return [
                v => !!v || 'Name is required',
            ]
        },
        emailRules() {
            return [
                v => !!v || 'E-mail is required',
                v => /^(([^<>()[\]\\.,;:\s@']+(\.[^<>()\\[\]\\.,;:\s@']+)*)|('.+'))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(v) || 'E-mail must be valid',
            ]
        }, 
        passwordRules(){
            return [
                v => !!v || 'Password is required',
                v => (v && v.length >= 5) || 'Password must have 5+ characters' 
            ]
        }, 
        photoRules(){
            return [
                v => (v != null && v.length != 0)  || 'Photo is required',
            ]
        }
    },
    methods: {
        ...mapActions({
            setAlert : 'alert/set',
            // setToken : 'auth/setToken'
        }),
        close() {
            this.$emit('closed', false)
        },
        Preview_image() {
            if(this.image != null){
                this.url= URL.createObjectURL(this.image)
            }
        },
        submit(){
            this.onLoad = true;
            let formData = new FormData()
            let file = this.$refs.photo.$refs.input.files[0]
            formData.append('email', this.email)
            formData.append('password', this.password)
            formData.append('name', this.name)
            formData.append('photo_profile', file)
            
            const config = {
                method: "post",
                url: `${this.apiDomain}/api/v2/auth/register`,
                headers: {
                    'Accept': 'application/json'
                },
                data: formData
            };
            this.axios(config)
                .then(() => {
                    // const config = {
                    //     method: "post",
                    //     url: `${this.apiDomain}/api/v2/auth/login`,
                    //     data: {
                    //         'email': response.data.user.email,
                    //         'password': this.password
                    //     }
                    // };
                    // this.axios(config)
                        // .then((response) => {
                            // this.setToken(response.data.access_token)
                            this.setAlert({
                                status: true,
                                color: 'success',
                                text: 'Register Berhasil',
                            })
                            this.onLoad = false
                            this.email = ''
                            this.password = ''
                            this.image = [] 
                            this.name = ''
                            this.close()
                        })
                        // .catch((error) => {
                        //     console.log(error)
                        //     this.setAlert({
                        //         status: true,
                        //         color: 'error',
                        //         text: 'Login Gagal',
                        //     })
                        // })
                // })
                .catch((error) => {
                    console.log(error)
                    this.setAlert({
                        status: true,
                        color: 'error',
                        text: 'Register Gagal Email sudah dipakai',
                    })
                    this.onLoad = false
                })
        }
    },
}
</script>