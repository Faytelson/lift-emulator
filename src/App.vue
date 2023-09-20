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
        ></div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  components: {},
  mounted() {
    this.$refs.lift.style.bottom = `${this.current * 100}px`;
  },
  data() {
    return {
      stages: [
        {
          id: 0
        },
        {
          id: 1
        },
        {
          id: 2
        },
        {
          id: 3
        },
        {
          id: 4
        },
        {
          id: 5
        }
      ],
      callStack: [],
      vacant: true,
      current: 1,
      currentActive: 1,
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
      console.log('callStack', this.callStack)
    },
    launchLift(id, button) {
      const self = this;
      this.currentActive = id;
      this.vacant = false;
      let delta = Math.abs(id - this.current);
      // let animationTime = Math.abs(parseFloat(this.$refs.lift.style.bottom) - id * 100);
      let interval = setInterval(function () {
        if (self.current > id) {
          self.$refs.lift.style.bottom = `${parseFloat(self.$refs.lift.style.bottom) - 1}px`;
        } else {
          self.$refs.lift.style.bottom = `${parseFloat(self.$refs.lift.style.bottom) + 1}px`;
        }
        if (self.$refs.lift.style.bottom === `${id * 100}px`) {
          clearInterval(interval)
        }
      }, 10);
      setTimeout(function () {
        setTimeout(function () {
          // self.$refs.lift.style.bottom = `${id * 100}px`;
          self.vacant = true;
          button.style.background = 'white';
          self.callStack.shift(self.callStack[0]);
          self.check();
        }, 3000)
        self.current = id;
      }, delta * 1000)
    },
    check() {
      const buttons = document.querySelectorAll('.stages__stage-button');
      if (this.callStack.length > 0) {
        this.callLift(this.callStack[0], buttons[this.callStack[0] - 1]);
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
</style>
