<template>
  <div id="app">
    <div class="container">
      <div class="stages">
        <div
            class="stages__stage"
            v-for="stage in stages"
            :key="stage.id"
        >{{ stage.id }}
          <button
              class="stages__stage-button"
              :key="stage.id"
              v-if="stage.id !== 0"
              @click="callLift(stage.id, $event.target)"
          >Call lift
          </button>
        </div>
      </div>
      <div class="lift-track">
        <div
            class="lift-track__lift"
            ref="lift"
        >
          <div
              class="table"
          >
            <div
                v-show="tableClass"
                class="table__direction"
                :class="tableClass"
            ></div>
            <div class="table__stage">{{ currentActive }}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  components: {},
  mounted() {
    this.checkSessionState();
    this.$refs.lift.style.bottom = `${this.current * 100}px`;
  },
  data() {
    return {
      stages: [
        {id: 0},
        {id: 1},
        {id: 2},
        {id: 3},
        {id: 4},
        {id: 5}
      ],
      callStack: [],
      vacant: true,
      current: 1,
      currentActive: 1,
      tableClass: ''
    }
  },
  methods: {
    callLift(id, target) {
      if (this.currentActive !== id) {
        target.style.background = 'yellow';
        if (!this.callStack.includes(id)) {
          this.callStack.push(id)
        }
        if (this.vacant) {
          this.launchLift(this.callStack[0], target);
        }
      }
    },
    launchLift(id, button) {
      const self = this;
      this.currentActive = id;
      localStorage.setItem('currentActive', self.currentActive);
      this.vacant = false;
      let delta = Math.abs(id - this.current);
      // setting lift's position
      let interval = setInterval(function () {
        if (self.current > id) {
          self.tableClass = 'down';
          self.$refs.lift.style.bottom = `${parseFloat(self.$refs.lift.style.bottom) - 1}px`;
          if (self.$refs.lift.style.bottom <= `${id * 100}px`) {
            clearInterval(interval)
          }
        } else {
          self.tableClass = 'up';
          self.$refs.lift.style.bottom = `${parseFloat(self.$refs.lift.style.bottom) + 1}px`;
          if (self.$refs.lift.style.bottom >= `${id * 100}px`) {
            clearInterval(interval)
          }
        }
      }, 10);
      //set timer depending on animation time
      setTimeout(function () {
        setTimeout(function () {
          self.vacant = true;
          button.style.background = 'white';
          self.callStack.shift(self.callStack[0]);
          self.checkStack();
        }, 3000)
        self.current = id;
        localStorage.setItem('current', self.current);
        self.tableClass = '';
        self.animate(button);
      }, delta * 1000)
    },
    checkStack() {
      const buttons = document.querySelectorAll('.stages__stage-button');
      if (this.callStack.length > 0) {
        this.callLift(this.callStack[0], buttons[this.callStack[0] - 1]);
      }
    },
    animate(button) {
      let counter = 0;
      let intervalFlashing = setInterval(function () {
        button.classList.toggle('blue');
        counter += 0.5;
        if(counter >= 3) {
          clearInterval(intervalFlashing);
          button.classList.remove('blue');
        }
      }, 500)
    },
    checkSessionState() {
      if(localStorage.getItem('current')) {
        this.current = localStorage.getItem('current');
      }
      if(localStorage.getItem('currentActive')) {
        this.currentActive = localStorage.getItem('currentActive');
      }
    }
  }
}
</script>

<style lang="scss">
*,
*::after,
*::before {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

input:focus {
  outline: none;
}

button {
  outline: none;
  border: none;
}

.container {
  position: relative;
}

.stages {
  width: 80px;
  display: flex;
  flex-direction: column-reverse;

  &__stage {
    width: 100%;
    height: 100px;
    background-color: pink;
    border-bottom: 1px solid #000;
    position: relative;
  }

  &__stage-button {
    position: absolute;
    left: 100%;
    top: 0;
    width: 50px;
    height: 50px;
    border-radius: 50%;
    cursor: pointer;
    border: 1px solid #000;
  }
}

.lift-track {
  position: absolute;
  width: 80px;
  height: 100%;
  bottom: 0;
  left: 0;

  &__lift {
    height: 100px;
    width: 100%;
    background-color: aqua;
    position: absolute;
    bottom: 100px;
  }
}

.table {
  display: flex;
  align-items: center;
  flex-direction: column;
  gap: 10px;

  &__direction {
    height: 30px;
    width: 15px;

    &.down {
      background: url("~@/assets/icon_arrow_down.svg") center / contain no-repeat;
    }

    &.up {
      background: url("~@/assets/icon_arrow_up.svg") center / contain no-repeat;
    }
  }

  &__stage {
    font-size: 20px;
  }
}

.blue {
  background-color: #DAF0FF !important;
}
</style>
