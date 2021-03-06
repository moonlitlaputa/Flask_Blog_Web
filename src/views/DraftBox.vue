<template>
    <div>
        <left-col></left-col>
        <v-container>
            <v-layout>
                <v-flex xs12 md9 offset-md3>
                    <article-list :articles="articles" v-if="load"></article-list>
                    <pagination
                        :initPage="page"
                        :length="length"
                        v-if="load"
                        @pageChange="bindPageChange"
                    ></pagination>
                </v-flex>
            </v-layout>
            <v-speed-dial
                v-model="fab"
                right
                bottom
                direction="top"
                :open-on-hover="desktop"
                style="position: fixed"
                v-resize="getHoverAttr"
            >
                <v-btn slot="activator" v-model="fab" color="blue darken-2" fab>
                    <v-icon>apps</v-icon>
                    <v-icon>close</v-icon>
                </v-btn>
                <v-btn fab small color="pink lighten-3" @click="$vuetify.goTo(0)">
                    <v-icon>arrow_upward</v-icon>
                </v-btn>
                <v-btn fab small color="error" v-if="getLoginState" @click="logout">
                    <v-icon>exit_to_app</v-icon>
                </v-btn>
                <v-btn
                    fab
                    small
                    color="success"
                    class="fab"
                    v-if="addArticlePermission"
                    @click="addArticle"
                >
                    <v-icon>create</v-icon>
                </v-btn>
                <v-btn fab small color="info" class="fab" @click="profile">
                    <v-icon>person</v-icon>
                </v-btn>
            </v-speed-dial>
        </v-container>
    </div>
</template>

<script>
import LeftCol from "@/components/LeftCol";
import ArticleList from "@/components/article/ArticleList";
import Pagination from "@/components/Pagination";
import userAgent from "@/libs/userAgent";
import { api } from "@/libs/api";

export default {
    name: "draftBox",
    data() {
        return {
            fab: false,
            desktop: true,
            articles: [],
            length: 0,
            load: false,
            page: 1,
        };
    },
    components: {
        LeftCol: LeftCol,
        articleList: ArticleList,
        pagination: Pagination
    },
    methods: {
        profile() {
            this.$router.push({ name: "profile" });
        },
        logout() {
            this.$store.commit("logout");
            this.$cookie.delete("token");
            this.$store.commit("showSnackbar", {
                text: "Logout Success",
                color: "success"
            });
        },
        getHoverAttr() {
            if (userAgent.isMobile()) {
                this.desktop = false;
            } else {
                this.desktop = true;
            }
        },
        addArticle() {
            this.$router.push({ name: "addArticle" });
        },
        async getArticle(page) {
            this.axios
                .get(api.posts, {
                    params: {
                        page: page,
                        draft: true
                    }
                })
                .then(response => {
                    this.articles = response.data.posts;
                    this.length = response.data.pages;
                });
        },
        async loadArticle(page) {
            await this.getArticle(page);
            this.load = true;
        },
        bindPageChange(page) {
            this.$router.push({ name: "draftBox", params: { page: page } });
        },
    },
    computed: {
        addArticlePermission() {
            if (this.$store.state.permission >= 255) {
                return true;
            } else {
                return false;
            }
        },
        getLoginState() {
            if (this.$store.state.login) {
                return true;
            } else {
                return false;
            }
        }
    },
    mounted() {
        let page = this.$route.params.page;
        this.getHoverAttr();
        if (!page) {
            this.loadArticle(1)
        } else {
            this.page = parseInt(page)
            this.loadArticle(this.page);
        }
    },
    watch: {
        "$route": function(val) {
            let page = val.params.page
            if (page) {
                this.page = parseInt(page)
                this.loadArticle(this.page)
            } else {
                this.loadArticle(1)
            }
            this.$vuetify.goTo(0)
        }
    }
};
</script>

<style scoped>
.home {
    margin-left: 0% !important;
    padding-left: 0% !important;
    padding-right: 0% !important;
}
</style>
