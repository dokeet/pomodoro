<template>
  <div class="columns is-mobile">
    <div class="column">
      <section class="hero is-fullheight is-primary">
        <div class="hero-body">
          <div class="container is-fullwidth has-text-centered">
            <p>Status: {{state}} {{sessions}} {{breaks}} | {{date}} - {{finish}}</p>
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
              <div v-show="!status" id="buttons">
                <a class="button is-primary" :class="{'is-disabled': sessionStarted}" @click="startSession">Start session</a>
                <a class="button is-primary" :class="{'is-disabled': !sessionStarted}" @click="stop">Stop</a>
              </div>
              <div v-show="status" id="buttons">
                <a class="button is-primary" :class="{'is-disabled': breakStarted}" @click="startBreak">Start break</a>
                <a class="button is-primary" :class="{'is-disabled': !breakStarted}" @click="stop">Stop</a>
              </div>
            </div>
          </div>
      </section>
    </div>
  </div>    
</template>

<script>
var time;

export default {

  mounted() {
    this.askNotify()
  },
  data()  {
    return {
      sessionTime: 10,
      state: 'Idle',
      status: false,
      breaks: 0,
      sessions: 0,
      sessionStarted: false,
      breakStarted: false,
      date: '',
      finish: ''
    }
  },
   methods: {
      sessionTimer() {
        if(this.sessionTime === 0){
            clearInterval(time)
            var d = new Date()
            this.finish = `${d.getHours()}:${d.getMinutes()}`
            this.sessions++;
            this.sessionStarted = false
            this.status = true
            this.state = 'Idle'
            this.notify()
        }else{
          return this.sessionTime--;
        }

      },
      breakTimer() {
        if(this.sessionTime === 0){
            clearInterval(time)
            var d = new Date()
            this.finish = `${d.getHours()}:${d.getMinutes()}`
            this.breakStarted = false
            this.status = false
            this.state = 'Idle'
            this.breaks++
            
        }else{
          return this.sessionTime--;
        }

      },
      startSession() {  
        var vm = this
        var d = new Date()
        clearInterval(time)
        this.sessionTime = 10
        this.date = `${d.getHours()}:${d.getMinutes()}`
        time = setInterval(vm.sessionTimer, 1000)
        vm.sessionStarted = true
        this.state = 'Working'
      },
      stop(){
        clearInterval(time)
        this.state = 'Stopped.'
        this.sessionStarted = false
      },
      reset(){
        clearInterval(time)
        this.sessionTime = 1500
      },
      startBreak(){
        clearInterval(time) 
        this.sessionTime = 300 
        time = setInterval(this.breakTimer, 1000)
        this.breakStarted = true
        this.state = 'Break'
      },
      up(){
        this.sessionTime = this.sessionTime + 60
      },
      down(){
        this.sessionTime = this.sessionTime - 60
      },
      notify () {
        var options = {
          icon: 'src/assets/logo.png'
        }

            // Let's check whether notification permissions have already been granted
          if (Notification.permission === "granted") {
            // If it's okay let's create a notification
            var notification = new Notification("Time to break", options);
          }

          // Otherwise, we need to ask the user for permission
          else if (Notification.permission !== 'denied') {
            Notification.requestPermission(function (permission) {
              // If the user accepts, let's create a notification
              if (permission === "granted") {
                var notification = new Notification("Time to break", options);
              }
            });
          }
      },
      askNotify(){
        Notification.requestPermission().then(function(result) {
          console.log(result);
        })
      }

      
    },
    computed: {
      minutes() {
        return Math.floor((((this.sessionTime % 31536000) % 86400) % 3600) / 60)
      },
      seconds(){
        return (((this.sessionTime % 31536000) % 86400) % 3600) % 60
      }
    }
  }

  
</script>

<style lang="sass" src="../../node_modules/bulma/bulma.sass">
</style>
<style scoped>
div.clickable {
  cursor: pointer;
}
h1 {
  font-size: 9em;
  font-weight: bold;
}
h2 {
  font-size: 9em;
}
</style>

