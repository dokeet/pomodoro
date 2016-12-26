<template>
   <div class="hero is-fullheight">   
        <div class="container has-text-centered is-mobile">	
            <div class="primary-buttons">
                <button class="button is-primary" @click="openModal">About</button>
                <button class="button is-primary" @click="session">Pomodoro</button>
                <button class="button is-primary" @click="shortBreak">Short Break</button>
                <button class="button is-primary" @click="longBreak">Long Break</button>

                <modal v-if="modalToggle">
                    <div class="card has-text-left is-pulled-left">
                        <div class="card-content">                                                    
                            <div class="media-content">                                                          
                                <p class="title is-2">Pomodoro Technique</p>
                            </div>
														<br>	
                            <div class="content">
                            The Pomodoro Technique is a time management method developed by Francesco Cirillo in the late 1980s. 
                            The technique uses a timer to break down work into intervals, traditionally 25 minutes in length, 
                            separated by short breaks. These intervals are named pomodoros, 
                            the plural in English of the Italian word pomodoro (tomato), 
                            after the tomato-shaped kitchen timer that Cirillo used as a university student.
                            <br>
                            <a class="twitter" href="https://en.wikipedia.org/wiki/Pomodoro_Technique" target="_blank">Read more</a>
                            </div>
														<footer>
															<small class="twitter is-pulled-right">
																made by <a href="https://twitter.com/dokstep">@dokstep</a>
															</small>
														</footer>

                        </div>
                    </div>
                    <button slot="close" class="modal-close" @click="openModal"></button>
                </modal>
                <p class="auto_start">
                    Status: {{state}}
                </p> 
                    
                <span>
                    Auto start timer: 
                    <input type="checkbox" v-model="autoStart" class="checkbox">			
                </span>   
            <div id="time"> 
                <div v-show="!sessionStarted" class="icon is-large clickable" @click="up">
                        <i class="button is-primary fa fa-caret-up" ></i>
                </div>

                    <h1 class="title">{{minutes}}</h1>
                    <h2 class="subtitle" >{{seconds}}</h2>
                    <progress class="progress is-primary is-small" :value="progress" :max="timeMax"></progress>
                    <div v-show="!sessionStarted" class="icon is-large clickable" @click="down">
                        <i class="button is-primary fa fa-caret-down" ></i>
                    </div>
                </div>
                <div class="buttons"> 
                    <a class="button is-primary is-large" :class="{'is-disabled': sessionStarted}" @click="start">Start</a>
                    <a class="button is-primary is-large" :class="{'is-disabled': !sessionStarted}" @click="stop">Stop</a>
                    <a class="button is-primary is-large" :class="{'is-disabled': sessionStarted}" @click="reset">Reset</a>
                </div>
        </div>
    </div> 
	
</template>

<script>
import modal from '../components/modal.vue'

    var time;

    export default {

        mounted() {
            
        },
        data() {
            return {
                sessionTime: 1500,
                timeMax: 1500,
                state: 'Idle',
                status: false,
                autoStart: false,
                sessionStarted: false,
                date: '',
                finish: '',
                modalToggle: false
            }
        },
        methods: {
            timer() {
                this.title()     
                if (this.sessionTime === 0) {
                    clearInterval(time)
                    var d = new Date()
                    this.finish = d.getMinutes()
                    this.sessionStarted = false
                    this.status = true
                    this.state = 'Idle'
                    this.notify('Finished')
                } else {
                    return this.sessionTime--;
                }
            },
            title(){
                document.title = `Pomodoro ${this.minutes}:${this.seconds}` || 'Pomodoro'
            },
            session() {
                clearInterval(time)
                this.sessionTime = 1500
                this.timeMax = 1500
                this.state = 'Idle'
                if (this.autoStart) {
                    this.start()
                    this.state = 'Started.'
                }
            },
            start() {
                if (this.sessionTime === 0) {
                    this.sessionStarted = false
                } else {
                    this.state = 'Started.'
                    var d = new Date()
                    this.sessionStarted = true
                    this.date = d.getMinutes()
                    time = setInterval(this.timer, 1000)            
                }
            },
            stop() {
                clearInterval(time)
                this.state = 'Stopped.'
                this.sessionStarted = false
            },
            reset() {
                clearInterval(time)
                this.sessionTime = 1500
                this.sessionStarted = false
            },
            shortBreak() {
                clearInterval(time)
                this.sessionTime = 300
                this.timeMax = 300
                this.sessionStarted = false
                this.state = 'Short break.'
                if (this.autoStart) {
                    this.start()
                }
            },
            longBreak() {
                clearInterval(time)
                this.sessionTime = 900
                this.timeMax = 900
                this.state = 'Long break. Get coffee.'
                this.sessionStarted = false
                if (this.autoStart) {
                    this.start()
                }
            },
            up() {
                this.sessionTime = this.sessionTime + 60
            },
            down() {
                if (this.sessionTime <= 59) {
                    this.sessionTime = 0
                } else {
                    this.sessionTime = this.sessionTime - 60
                }
            },
            notify(text) {
                console.log(this)
                var options = {
                    icon: 'src/assets/tomato.png',
                }
                // Let's check whether notification permissions have already been granted
                if (Notification.permission === "granted") {
                    // If it's okay let's create a notification
                    var notification = new Notification(text, options);
                    var audio = new Audio('src/assets/audio/bloop_x.wav')
                    audio.play()
                }
                // Otherwise, we need to ask the user for permission
                else if (Notification.permission !== 'denied') {
                    Notification.requestPermission(function(permission) {
                        // If the user accepts, let's create a notification
                        if (permission === "granted") {
                            var notification = new Notification(text, options);
                        }
                    });
                }
            },
            askNotify() {
                Notification.requestPermission().then(function(result) {
                    console.log(result);
                })
            },
            openModal(){
                this.modalToggle = !this.modalToggle
            }
        },
        computed: {
            minutes() {
                return Math.floor((((this.sessionTime % 31536000) % 86400) % 3600) / 60)
            },
            seconds() {
                if(((((this.sessionTime % 31536000) % 86400) % 3600) % 60) >= 10){
                    return ((((this.sessionTime % 31536000) % 86400) % 3600) % 60)
                } else {
                    return '0'+((((this.sessionTime % 31536000) % 86400) % 3600) % 60)
                }
            },
            progress(){
                return (this.timeMax - this.sessionTime) 
            },

        },
        components: {
            modal

        }
    }
</script>

<style lang="sass" src="../../node_modules/bulma/bulma.sass"></style>
<style lang="sass">
.twitter a {
	color: #F62554;
}
.content {
	color: #c46f7f;
}
.content a:not(.button):visited {
    color: #F62554;
}
.card-content {
	background-color: #1A1B3A;
}
.text {
    color: #1A1B3A;
}
.hero {
    background-color: #1A1B3A;
}

html {
    max-height: 100vh;
    max-width: 100vw;
}

p,
span {
    color: #F62554;
    font-size: 24px
}

.title {
    font-weight: bold;
}

.title,
.subtitle {
    font-size: 20vh;
    color: #F62554 
}

/*#F62554*/

.primary-buttons {
    padding-bottom: 25px;
    padding-top: 10px;
}

div.clickable {
    cursor: pointer;
}

.hero .nav {
    background: none;
    box-shadow: 0 0px 0 rgba(0, 0, 0, 0);
}
</style>