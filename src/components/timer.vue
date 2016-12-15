<template>
	<div class="container has-text-centered is-mobile">	
		<div class="primary-buttons">
			<button class="button is-primary" @click="session">Pomodoro</button>
			<button class="button is-primary" @click="shortBreak">Short Break</button>
			<button class="button is-primary" @click="longBreak">Long Break</button>
			<p class="auto_start">
				Status: {{state}}
			</p> 
			<span>
				Auto start timer: 
				<input type="checkbox" v-model="autoStart" class="checkbox">			
			</span>
		</div>
		<div id="time"> 
			<div v-show="!sessionStarted" class="icon is-large clickable" @click="up">
					<i class="button is-primary fa fa-caret-up" ></i>
				</div>
				<h1 class="title">       
						{{minutes}}
				</h1>
				<h2 class="subtitle">
						{{seconds}}
				</h2>
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
</template>

<script>
    var time;

    export default {

        mounted() {
            this.askNotify()
        },
        data() {
            return {
                sessionTime: 1500,
                state: 'Idle',
                status: false,
                autoStart: false,
                sessionStarted: false,
                breakStarted: false,
                date: '',
                finish: ''
            }
        },
        methods: {
            timer() {
                if (this.sessionTime === 0) {
                    clearInterval(time)
                    var d = new Date()
                    this.finish = `${d.getHours()}:${d.getMinutes()}`
                    this.sessionStarted = false
                    this.status = true
                    this.state = 'Idle'
                    this.notify('Finished')
                } else {
                    return this.sessionTime--;
                }
            },
            session() {
                clearInterval(time)
                this.sessionTime = 1500
                this.state = 'Pomodoro.'
                if (this.autoStart) {
                    this.start()
                    this.state = 'Working!'
                }
            },
            start() {
                if (this.sessionTime === 0) {
                    this.sessionStarted = false
                } else {
                    this.state = 'Working!'
                    var d = new Date()
                    this.sessionStarted = true
                    this.date = `${d.getHours()}:${d.getMinutes()}`
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
                this.sessionStarted = false
                this.state = 'Short break.'
                if (this.autoStart) {
                    this.start()
                }
            },
            longBreak() {
                clearInterval(time)
                this.sessionTime = 900
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
            }
        },
        computed: {
            minutes() {
                return Math.floor((((this.sessionTime % 31536000) % 86400) % 3600) / 60)
            },
            seconds() {
                return (((this.sessionTime % 31536000) % 86400) % 3600) % 60
            },
        }
    }
</script>

<style lang="sass" src="../../node_modules/bulma/bulma.sass"></style>
<style lang="sass">
    html {
        background-color: #1A1B3A;
        max-height: 100vh;
        max-width: 100vw;
    }
    
    p,
    span {
        color: #b33353;
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
    
    .primary-buttons {
        padding-bottom: 25px;
        padding-top: 10px;
    }
    
    div.clickable {
        cursor: pointer;
    }
</style>