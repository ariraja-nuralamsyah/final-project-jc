<template>
    <v-card>
        <v-toolbar dark color="primary">
            <v-btn icon dark @click.native="close">
                <v-icon>mdi-close</v-icon>
            </v-btn>
            <v-toolbar-title>Login</v-toolbar-title>
        </v-toolbar>
        <v-divider></v-divider>
        <v-container fluid>
            <v-form ref="form" v-model="isValid">
                <v-text-field
                    v-model="email"
                    label="E-mail"
                    :rules="emailRules"
                    required
                    append-icon="mdi-email"
                ></v-text-field>
                <v-text-field
                    v-model="password"
                    :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off' "
                    :type="showPassword ? 'text' : 'password' "
                    label="Password"
                    :rules="passwordRules"
                    counter
                    @click:append="showPassword = !showPassword"
                ></v-text-field>

                <div class="text-xs-center">
                    <v-btn
                        color="success lighten-1"
                        @click="submit"
                        :disabled="!isValid"
                    >
                        Login &nbsp;
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
            onLoad: false,
            isValid: true,
            apiDomain: 'https://demo-api-vue.sanbercloud.com',
        }
    },
    computed: {
        emailRules() {
            return [
                v => !!v || 'E-mail is required'
            ]
        }, 
        passwordRules(){
            return [
                v => !!v || 'Password is required'
            ]
        }
    },
    methods: {
        ...mapActions({
            setAlert : 'alert/set',
            setToken : 'auth/setToken'
        }),
        close() {
            this.$emit('closed', false)
        },
        submit(){
            this.onLoad = true
            const config = {
                method: "post",
                url: `${this.apiDomain}/api/v2/auth/login`,
                data: {
                    'email': this.email,
                    'password': this.password
                }
            };

            this.axios(config)
                .then((response) => {
                    this.setToken(response.data.access_token)
                    this.setAlert({
                        status: true,
                        color: 'success',
                        text: 'Login Berhasil',
                    })
                    this.onLoad = false
                    this.close()
                })
                .catch(() => {
                    this.setAlert({
                        status: true,
                        color: 'error',
                        text: 'Email/Password salah',
                    })
                    this.onLoad = false
                })
        }
    },
}
</script>