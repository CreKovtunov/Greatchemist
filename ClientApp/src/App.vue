<template>
    <div class="wh100">
        <v-app id="inspire" style="background: transparent;">
            <v-main>
                <router-view></router-view>
                <app-snackbar></app-snackbar>
            </v-main>
        </v-app>
</div>
</template>

<script>
    export default {
        name: 'App',

        data: () => ({}),

        methods: {
            comeIn() {
                    let u = {
                        email: this.$store.state.email,
                        password: this.$store.state.password
                    }
                    this.$http
                        .post(this.$store.state.baseUrl + `api/user/authorization`, u)
                        .then(response => {
                            if (this.$store.state.isAuthorized != response.data.isAuthorized) {
                                this.$store.state.isAuthorized = response.data.isAuthorized
                            }
                            if (this.$store.state.isTeacher != response.data.isTeacher) {
                                this.$store.state.isTeacher = response.data.isTeacher
                            }
                            if (this.$store.state.isManager != response.data.isManager) {
                                this.$store.state.isManager = response.data.isManager
                            }

                            if (!response.data.isAuthorized) {
                                this.$store.state.snackbarShow = false
                                this.$store.state.snackbarColor = "#ff5252"
                                this.$store.state.snackbarText = "Введены неверные e-mail или пароль"
                                this.$store.state.snackbarShow = true
                                this.$router.push({ path: '/SignIn' })
                            }
                        })
                        .catch(e => {
                            this.$store.state.snackbarShow = false
                            this.$store.state.snackbarColor = "#ff5252"
                            this.$store.state.snackbarText = "Ошибка авторизации"
                            this.$store.state.snackbarShow = true
                            this.$router.push({ path: '/SignIn' })

                            console.log(e)
                        });
            },
        },

        created() {
            if (this.$store.state.baseUrl[this.$store.state.baseUrl.length - 1] != '/') {
                this.$store.state.baseUrl = this.$store.state.baseUrl + '/'
            }
        },

        watch: {
            '$store.state.email'() {
                this.comeIn();
            },
            '$store.state.isAuthorized'() {
                this.comeIn();
            },
            '$store.state.isTeacher'() {
                this.comeIn();
            },
            '$store.state.isManager'() {
                this.comeIn();
            },
        },
    };
</script>

<style>
    html {
        height: 100%;
        overflow: hidden !important;
        width: 100%;
        background-image: url('assets/bkg1.png');
        background-size:cover;
    }

    body, .wh100 {
        height: 100%;
        overflow: auto !important;
        width: 100%;
    }

    div {
        font-family: "helioscond","Segoe Ui Light","Segoe Ui", "Segoe",Tahoma,Helvetica,Arial,sans-serif !important;
    }

    .clearfix::after {
        content: "";
        clear: both;
        display: table;
    }

    /*.appTitle {
        font: 23px/130% "helioscond","Segoe Ui Light","Segoe Ui", "Segoe",Tahoma,Helvetica,Arial,sans-serif;
        color: rgba(0,0,0,.8) !important;
        text-decoration: none;
        font-weight: 700;
    }

        .appTitle:hover {
            text-decoration: underline;
        }

    .pageWrap {
        height: 100%;
        position: relative;
        width: 100%;
    }

    .rightPart {
        background: #fafafa;
        box-sizing: border-box;
        height: 100%;
        overflow: hidden;
        padding: 0px 0px 0px 0px;
        position: absolute;
        width: 100%;
        transition: padding 0.5s;
    }

    .rightPartContentWrap {
        height: 100%;
        overflow: auto;
        width: 100%;
    }

    .rightPartContent {
        min-width: 724px;
        padding: 15px 25px 25px;
    }

    .main-title {
        border-bottom: 1px solid #1565C0;
        color: #424245;
        font-family: "helioscond","Segoe Ui Light","Segoe Ui", "Segoe",Tahoma,Helvetica,Arial,sans-serif;
        font-size: 25px;
        line-height: 40px;
        margin-bottom: 15px !important;
        margin-top: 0px;
    }

    .main-title2 {
        color: #424245;
        font-family: "helioscond","Segoe Ui Light","Segoe Ui", "Segoe",Tahoma,Helvetica,Arial,sans-serif;
        font-size: 30px;
        line-height: 50px;
        margin-bottom: 25px !important;
        margin-top: 10px;
        padding-left: 10px;
        text-align: center;
    }

    .label-wrap {
        float: left;
        width: 30%;
        display: flex;
        min-height: 40px;
    }

        .label-wrap label {
            font-weight: bold;
            margin: auto 0;
        }

    .field-wrap {
        float: left;
        width: 70%;
    }*/

    .v-input--checkbox, .v-input--checkbox .v-label {
        margin: 0px !important;
    }

    .custom-disabled .v-input__append-inner {
        display: none !important;
    }

    .menu-point {
        text-decoration: none !important;
    }

    .fs14 label {
        font-size: 14px;
    }

    .chip-wrapper .field-wrap {
        position: relative;
    }

    .chips {
        background: white;
        border: 1px solid #bebebe;
        -webkit-border-radius: 4px;
        -moz-border-radius: 4px;
        border-radius: 4px;
        margin-right: 33px;
        margin-bottom: 9px;
        min-height: 38px;
        padding: 5px 15px 0px;
    }

        .chips:hover {
            border: 1px solid #242424;
        }

        .chips .v-chip {
            margin: 0 10px 5px 0;
        }

    .chip-edit {
        position: absolute !important;
        right: 0 !important;
        top: 5px !important;
    }

    .v-text-field__details {
        display: none !important;
    }

    .v-input__control {
        margin-bottom: 5px;
    }

    .v-select .v-chip {
        margin: 4px !important;
    }

    .v-autocomplete:not(.v-input--is-focused).v-select--chips input {
        max-height: 64px !important;
    }

    /*.v-menu__content.menuable__content__active {
        margin-top: 27px;
    }*/
</style>