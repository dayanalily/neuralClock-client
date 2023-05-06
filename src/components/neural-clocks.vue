<template>
  <div class="row justify-content-md-center p-3">
    <div class="col-12 text-end">
      <button @click="showConfig = !showConfig" class="config-button">
        <span class="lnr lnr-cog"></span>
      </button>
      <div
        v-if="showConfig"
        class="modal-config-overlay"
        @click.self="showConfig = false"
      >
        <div class="modal-config text-start">
          <div class="row row-title">
            <div class="col-12 border-bottom">
              <h3 class="pl-2">Configuración</h3>
            </div>
            <div class="col-12">
              <div class="row mt-3">
                <div class="col-md-8">Duración Pomodoro (min):</div>
                <div class="col-md-4">
                  <input
                    type="number"
                    min="1"
                    v-model.number="form.pomodoroDuration"
                  />
                </div>
              </div>
              <div class="row mt-3">
                <div class="col-md-8">Duración pausa corta (min):</div>
                <div class="col-md-4">
                  <input
                    type="number"
                    min="1"
                    v-model.number="form.shortBreakDuration"
                  />
                </div>
              </div>
              <div class="row mt-3 pb-3 border-bottom">
                <div class="col-md-8">Duración pausa larga (min):</div>
                <div class="col-md-4">
                  <input
                    type="number"
                    min="1"
                    v-model.number="form.longBreakDuration"
                  />
                </div>
              </div>
            </div>
          </div>
          <div class="modal-buttons">
            <button @click="showConfig = false" class="btn-cancelar">
              Cancelar
            </button>
            <button @click="changeDurations(form)">Guardar</button>
          </div>
        </div>
      </div>
    </div>
    <div class="col-md-5 shadow mt-2 bg-white rounded text-center">
      <div class="row p-4">
        <div class="col-12">
          <h1 class="title">NeuralCl<span class="lnr lnr-clock"></span>cks</h1>
          <div class="timer-container">
            <p>{{ timeLeft }}</p>
            <div class="circle-progress">
              <div
                class="circle"
                :style="{
                  background: `conic-gradient(${progressColor} 0% ${progress}%, lightgray ${progress}% 100%)`,
                }"
              ></div>
            </div>
          </div>

          <div class="action-buttons">
            <button @click="startPomodoro">Pomodoro</button>
            <button @click="startShortBreak">Pausa corta</button>
            <button @click="startLongBreak">Pausa larga</button>
          </div>
          <div class="border-top mt-4 pt-4">
            <button @click="clearAllInterval()" class="btn-cancelar">
              Detener
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      timeLeft: "00:00",
      progress: 0,
      progressColor: "green",
      timerDuration: 0,
      timerInterval: null,
      showConfig: false,
      pomodoroDuration: 25,
      shortBreakDuration: 2,
      longBreakDuration: 15,
      form: {
        pomodoroDuration: 25,
        shortBreakDuration: 2,
        longBreakDuration: 15,
      },
    };
  },
  methods: {
    startPomodoro() {
      if (!this.validateDuration(this.pomodoroDuration, "Pomodoro")) return;
      this.startTimer(this.pomodoroDuration);
      document.querySelector(".circle").classList.add("active");
    },
    startShortBreak() {
      if (!this.validateDuration(this.shortBreakDuration, "pausa corta"))
        return;
      this.startTimer(this.shortBreakDuration);
      document.querySelector(".circle").classList.add("active");
    },
    startLongBreak() {
      if (!this.validateDuration(this.longBreakDuration, "pausa larga")) return;
      this.startTimer(this.longBreakDuration);
      document.querySelector(".circle").classList.add("active");
    },

    startTimer(minutes) {
      clearInterval(this.timerInterval);
      const endTime = Date.now() + minutes * 60 * 1000;
      this.timerDuration = minutes;
      this.timerInterval = setInterval(() => {
        const timeRemaining = endTime - Date.now();
        if (timeRemaining <= 0) {
          clearInterval(this.timerInterval);
          this.timeLeft = "00:00";
          this.progress = 100;
          document.querySelector(".circle").classList.remove("active");
          return;
        }
        const secondsLeft = Math.ceil(timeRemaining / 1000);
        const minutesLeft = Math.ceil(secondsLeft / 60);
        console.log("minutesLeft", minutesLeft);
        this.timeLeft = this.formatTime(secondsLeft);
        this.progress =
          ((this.timerDuration - minutesLeft) / this.timerDuration) * 100;
        this.progressColor = this.progress < 85 ? "green" : "red";
      }, 1000);
    },
    formatTime(seconds) {
      const min = Math.floor(seconds / 60);
      const sec = seconds % 60;
      return `${min.toString().padStart(2, "0")}:${sec
        .toString()
        .padStart(2, "0")}`;
    },
    validateDuration(duration, timerType) {
      if (!duration || duration <= 0) {
        alert(`Por favor, configure un valor válido para ${timerType}`);
        return false;
      }
      return true;
    },
    clearAllInterval() {
      clearInterval(this.timerInterval);
      this.timeLeft = "00:00";
      this.progress = 0;
      document.querySelector(".circle").classList.remove("active");
      return;
    },
    changeDurations(form) {
      this.pomodoroDuration = form.pomodoroDuration;
      this.shortBreakDuration = form.shortBreakDuration;
      this.longBreakDuration = form.longBreakDuration;
      this.showConfig = false;
    },
  },
};
</script>
