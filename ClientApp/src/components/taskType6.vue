<template>
    <div style="height:100%;">
        <div v-if="!loaded" class="text-center">
            <v-progress-circular indeterminate
                                 color="#000"
                                 :size="70"></v-progress-circular>
        </div>
        <div style="position: relative;height:100%;" v-if="loaded && isTesting">
            <p>Оставшееся время: {{commonTimeFunc}}</p>
            <img :src="`${publicPath}images/` + question.questionLink" style="max-width: 100%; max-height: 80%;" />
            <div class="answerInputsWrap">
                <v-form>
                    <v-container>
                        <v-row>
                            <v-col cols="12"
                                   sm="4"
                                   v-if="question.description1">
                                <v-text-field v-model.toLowerCase.trim="userAnswer1"
                                              :label="question.description1"
                                              outlined></v-text-field>
                            </v-col>

                            <v-col cols="12"
                                   sm="4"
                                   v-if="question.description2">
                                <v-text-field v-model.toLowerCase.trim="userAnswer2"
                                              :label="question.description2"
                                              outlined></v-text-field>
                            </v-col>

                            <v-col cols="12"
                                   sm="4"
                                   v-if="question.description3">
                                <v-text-field v-model.toLowerCase.trim="userAnswer3"
                                              :label="question.description3"
                                              outlined></v-text-field>
                            </v-col>
                        </v-row>
                    </v-container>
                </v-form>
            </div>
            <div class="answerButtonsWrap">
                <v-btn x-large
                       color="#FFC702"
                       class="answerButton"
                       @click="addAnswer()">
                    Завершить
                </v-btn>
            </div>
        </div>
        <div style="position: relative;height:100%;" v-if="loaded && !isTesting">
            <p class="testText">Вы успешно завершили задание {{officialNumber}}</p>
            <p class="testText">Ваш резутат: {{rightCount}} из {{commonCount}}</p>
            <p class="testText">Затраченное время: {{timeFunc}}</p>
            <div class="answerButtonsWrap">
                <v-btn x-large
                       color="#FFC702"
                       class="answerButton"
                       @click="restart()">
                    Продолжить
                </v-btn>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'TaskType6',

        props: {
            questions: {
                type: Array,
                required: true
            },
            iterationId: {
                type: Number,
                required: true
            },
            officialNumber: {
                type: String,
                required: true
            },
            timeDB: {
                type: Number,
                required: true
            },
        },

        data: () => ({
            id: 0,
            question: null,
            startTime: new Date(),
            endTime: new Date(),
            isTesting: false,
            commonCount: 0,
            rightCount: 0,
            loaded: false,
            userAnswer1: null,
            userAnswer2: null,
            userAnswer3: null,
            timer: 0,
            time: 0,
            stopTimer: false,
            publicPath: process.env.BASE_URL,
        }),

        computed: {
            timeFunc: function () {
                let dif = this.endTime - this.startTime

                var seconds = Math.floor((dif / 1000) % 60),
                    minutes = Math.floor((dif / (1000 * 60)) % 60),
                    hours = Math.floor((dif / (1000 * 60 * 60)) % 24);

                hours = (hours < 10) ? "0" + hours : hours;
                minutes = (minutes < 10) ? "0" + minutes : minutes;
                seconds = (seconds < 10) ? "0" + seconds : seconds;

                return hours + ":" + minutes + ":" + seconds;
            },

            commonTimeFunc: function () {
                let dif = this.time

                var seconds = Math.floor((dif / 1) % 60),
                    minutes = Math.floor((dif / (1 * 60)) % 60),
                    hours = Math.floor((dif / (1 * 60 * 60)) % 24);

                hours = (hours < 10) ? "0" + hours : hours;
                minutes = (minutes < 10) ? "0" + minutes : minutes;
                seconds = (seconds < 10) ? "0" + seconds : seconds;

                return hours + ":" + minutes + ":" + seconds;
            },
        },

        methods: {
            loadPage() {
                this.stopTimer = false
                this.id = 0
                this.question = this.questions[this.id]
                this.$store.state.answers = []
                this.userAnswer1 = null
                this.userAnswer2 = null
                this.userAnswer3 = null
                this.startTime = new Date()
                this.isTesting = true
                this.time = this.timeDB
                this.loaded = true
                this.countdown()
            },

            countdown() {
                if (!this.stopTimer) {
                    this.time--
                    if (this.time < 0) {
                        this.addAnswer()
                    }
                    else {
                        this.timer = setTimeout(this.countdown, 1000)
                    }
                }
            },

            restart() {
                if (this.$store.state.parallel == 1) {
                    this.$emit("testEnded")
                } else {
                    this.$store.state.parallel = this.$store.state.parallel + 1
                    this.loadPage()
                }
            },

            addAnswer() {
                this.stopTimer = true
                let a = {
                    QuestionId: this.question.id,
                    UserAnswer1: this.userAnswer1,
                    UserAnswer2: this.userAnswer2,
                    UserAnswer3: this.userAnswer3,
                    IterationId: this.iterationId,
                    Parallel: this.$store.state.parallel
                }
                this.$store.state.answers.push(a)
                if (this.id < this.questions.length - 1) {
                    this.id++
                    this.question = this.questions[this.id]
                } else {
                    this.isTesting = false
                    this.loaded = false
                    this.endTime = new Date()

                    let res = {
                        startTime: this.startTime,
                        endTime: this.endTime,
                        answers: this.$store.state.answers,
                        task: this.$store.state.currentTask
                    }
                    this.$http
                        .post(this.$store.state.baseUrl + `api/test/addAnswersType6`, res)
                        .then(response => {
                            this.commonCount = this.$store.state.answers.length
                            this.rightCount = response.data
                            this.loaded = true
                        })
                        .catch(e => {
                            this.$store.state.snackbarShow = false
                            this.$store.state.snackbarColor = "#ff5252"
                            this.$store.state.snackbarText = "Ошибка сохранения ответов"
                            this.$store.state.snackbarShow = true
                            this.$router.push({ path: '/' })

                            console.log(e)
                        });
                }
            },
        },

        created() {
            this.loadPage()
        },
    };
</script>
