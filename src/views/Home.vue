<template>
    <div class="home">
        <aside class="left-content" v-if="!mobile">
            <div class="logo" v-if="$store.state.customColor === false">
                <div class="logo-image"></div>
                <div class="logo-text" :style="`color: ${$vuetify.theme.themes.dark.primary}`">
                    <p>Location</p>
                    <p>Estimatr</p>
                </div>
            </div>
            <div class="logo" v-else>
                <div class="logo-image" :style="`background-image: url(${$store.state.customColor.image})`"></div>
                <div class="logo-text"
                     :style="`background-image: linear-gradient(${$store.state.customColor.colorTop}, ${$store.state.customColor.colorBottom});`">
                    <p>Location</p>
                    <p>Estimatr</p>
                </div>
            </div>
            <v-navigation-drawer
                    class="drawer"
                    permanent
                    v-model="drawer"
                    color='primary'
                    dark>
                <v-list dense
                        nav
                        class="py-0">
                    <v-list-item two-line v-if="$store.state.realAccount">
                        <v-list-item-content>
                            <v-list-item-title class="account">
                                <router-link :to="'/user?id='+$store.state.user.uid">
                                    {{$store.state.realAccount.email}}
                                </router-link>
                            </v-list-item-title>
                            <v-list-item-subtitle class="logout" @click="logout()">Logout</v-list-item-subtitle>
                        </v-list-item-content>
                    </v-list-item>
                    <v-list-item v-else-if="$store.state.user !== null" :to="'user?id='+$store.state.user.uid">
                        <v-list-item-content>
                            <v-list-item-title>My Maps</v-list-item-title>
                        </v-list-item-content>
                    </v-list-item>
                    <v-list-item to="/" exact>
                        <v-list-item-icon>
                            <v-icon>home</v-icon>
                        </v-list-item-icon>

                        <v-list-item-content>
                            <v-list-item-title>Play</v-list-item-title>
                        </v-list-item-content>
                    </v-list-item>
                    <v-list-item to="/explore">
                        <v-list-item-icon>
                            <v-icon>explore</v-icon>
                        </v-list-item-icon>

                        <v-list-item-content>
                            <v-list-item-title>Explore Maps</v-list-item-title>
                        </v-list-item-content>
                    </v-list-item>
                    <v-list-item to="/create">
                        <v-list-item-icon>
                            <v-icon>map</v-icon>
                        </v-list-item-icon>

                        <v-list-item-content>
                            <v-list-item-title>Map Maker</v-list-item-title>
                        </v-list-item-content>
                    </v-list-item>
                    <v-list-item to="/settings">
                        <v-list-item-action>
                            <v-icon color="darken-1">settings</v-icon>
                        </v-list-item-action>
                        <v-list-item-title class="text--darken-1">Settings</v-list-item-title>
                    </v-list-item>
                    <div v-if="!$store.state.realAccount">
                        <v-divider></v-divider>
                        <v-list-item to="/login">
                            <v-list-item-title class="text--darken-1">Login</v-list-item-title>
                        </v-list-item>
                        <v-list-item to="/register">
                            <v-list-item-title class="text--darken-1">Register</v-list-item-title>
                        </v-list-item>
                    </div>
                </v-list>
            </v-navigation-drawer>
        </aside>
        <main class="middle-content">
            <router-view @loadedMaps="loaded" @snack="showSnack"></router-view>
        </main>
        <v-snackbar v-model="snack">{{ snackText }}
            <v-btn color="primary" text @click="snack = false">Close</v-btn>
        </v-snackbar>
        <div class="bottom-content">
            <v-bottom-navigation
                    shift
                    color='primary'
                    v-if="mobile"
                    grow>
                <v-btn to="/" class="bottom-button">
                    <span>Play</span>
                    <v-icon>home</v-icon>
                </v-btn>
                <v-btn to="/explore" class="bottom-button">
                    <span>Explore</span>
                    <v-icon>explore</v-icon>
                </v-btn>
                <v-btn to="/create" class="bottom-button">
                    <span>Make</span>
                    <v-icon>map</v-icon>
                </v-btn>
                <v-btn to="/settings" class="bottom-button">
                    <span>Settings</span>
                    <v-icon>settings</v-icon>
                </v-btn>
                <v-btn v-if="$store.state.realAccount" :to="`/user?id=${$store.state.realAccount.uid}`"
                       class="bottom-button">
                    <span>Profile</span>
                    <v-icon>face</v-icon>
                </v-btn>
                <v-btn v-else to="/login" class="bottom-button">
                    <span>Login</span>
                    <v-icon>account_circle</v-icon>
                </v-btn>
            </v-bottom-navigation>
        </div>
    </div>
</template>

<script>
    import ApiKey from "../js/ApiKey";

    export default {
        name: 'Home',
        components: {},
        data() {
            return {
                drawer: true,
                loggedIn: false,
                showFooter: false,
                snack: false,
                snackText: '',
                dialog: false,
                feedback: '',
                loadingFeedback: false,
            }
        },
        mounted() {
            // if (!ApiKey.customKey) {
            //     alert("The API key for this project was disabled, a custom API key is required to play now. There is more info in the settings page.");
            //     this.$router.push('/settings');
            // }
        },
        methods: {
            async sendFeedback() {
                this.loadingFeedback = true;
                if (this.feedback.length !== 0) {
                    await this.$store.dispatch('submitFeedback', this.feedback);
                    this.showSnack("Feedback sent!");
                } else {
                    this.showSnack("Feedback can't be empty");
                }
                this.loadingFeedback = false;
                this.dialog = false;
            },
            async logout() {
                await this.$store.dispatch('logout');
                this.showSnack("Logged out successfully");
            },
            showSnack(text) {
                this.snackText = text;
                this.snack = true;
            },
            loaded() {
                this.showFooter = true;
            }
        },
        watch: {},
        computed: {
            mobile() {
                return this.$store.state.windowWidth <= 840;
            }
        }
    }
</script>

<style scoped>
    .home {
        top: 0;
        position: absolute;
        width: 100%;
    }

    .left-content {
        padding-top: 60px;
        position: fixed;
    }

    .drawer {
        padding-top: 10px;
        border-bottom-right-radius: 10px;
        border-top-right-radius: 10px;
        min-height: 500px;
        box-shadow: 0 0 30px 0 rgba(0, 0, 0, 0.45);
    }

    .logo {
        display: flex;
        justify-content: space-between;
        width: 110px;
        margin-left: 30px;
    }

    .logo-image {
        width: 40px;
        height: 40px;
        background-image: url(../assets/favicon256.png);
        background-size: 60%;
        background-repeat: no-repeat;
        background-position: 50% 20%;
        transition: margin-left 0.3s;
    }

    .logo-text {
        font-size: 14px;
        background-image: linear-gradient(#0cde4d, #39d37a);
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        font-weight: 800;
        line-height: 110%;
        transition: opacity 0.3s;
        opacity: 1;
    }

    @media screen and (max-width: 840px) {
        .logo-image {
            margin-left: -25px;
        }

        .logo-text {
            opacity: 0;
        }

        .middle-content {
            margin-left: 0 !important;
            padding: 10px !important;
            padding-bottom: 150px !important;
            margin-top: 10px !important;
        }

        .footer {
            padding-bottom: 58px !important;
        }
    }

    .logo-text p {
        margin: 0;
    }

    .middle-content {
        flex-grow: 1;
        margin: 100px 0 15px 256px;
        padding: 10px 30px 100px;
    }

    .bottom-content {
        position: fixed;
        bottom: 0;
        width: 100%;
    }

    .bottom-button {
        /*padding: 9px !important;*/
        /*display: inline-block;*/
        height: 100% !important;
    }

    .footer {
        z-index: 0;
        padding-bottom: 0;
    }

    .bottom-row {
        display: flex;
        justify-content: space-between;
    }

    .buttons {

    }

    .buttons > * {
        margin-left: 10px;
    }

    .made-by {
        padding: 7px 0;
        display: inline-block;
    }

    .logout, .account {
        cursor: pointer;
        -webkit-touch-callout: none; /* iOS Safari */
        -webkit-user-select: none; /* Safari */
        -khtml-user-select: none; /* Konqueror HTML */
        -moz-user-select: none; /* Old versions of Firefox */
        -ms-user-select: none; /* Internet Explorer/Edge */
        user-select: none;
        /* Non-prefixed version, currently
                                         supported by Chrome, Opera and Firefox */
    }

    .account a {
        color: white;
    }

    .account:hover {
        text-decoration: underline;
    }
</style>
