<template>
    <div :class="loginView">
        <left-col></left-col>
        <v-container :class="loginContainer" v-resize="onResize">
            <v-layout align-center justify-center fill-height>
                <v-flex xs12 md6 lg4>
                    <login-card @login="redirectView"></login-card>
                </v-flex>
            </v-layout>
        </v-container>
    </div>
</template>

<script>
import LeftCol from "@/components/LeftCol";
import LoginCard from "@/components/LoginCard"
import userAgent from "@/libs/userAgent"
export default {
    data () {
        return {
            loginView: "loginViewDesktop",
            loginContainer: "loginDesktop"
        }
    },
    components: {
        leftCol: LeftCol,
        loginCard: LoginCard
    },
    mounted () {
        if (userAgent.isMobile()) {
            this.loginView = ""
            this.loginContainer = ""
        } else {

        }
    },
    methods: {
        onResize () {
            if (userAgent.isMobile()) {
                this.loginView = ""
                this.loginContainer = ""
            } else {
                this.loginView = "loginViewDesktop"
                this.loginContainer = "loginDesktop"
            }
        },
        redirectView () {
            if (this.$route.query.fromURL) {
                this.$router.push({path: this.$route.query.fromURL})
            } else {
                this.$router.push({name: 'home'})
            }
        }
    }
}
</script>

<style>
    .loginViewDesktop {
        height: 100%;
    }
    .loginDesktop {
        height: 100%;
        max-width: 80%;
        margin-left: 20%;
    }
</style>
