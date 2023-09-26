<template>
  <div class="container">
    <div class="stages">
      <div
          class="stages__stage"
          v-for="stage in stages"
          :key="stage.id"
      >{{ stage.id }}
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
</template>

<script>
export default {
  name: "LiftComponent",
  props: {
    stages: {
      type: Array,
    },
    num: {
      type: Number,
    },
    localObj: Object,
  },
  mounted() {
    this.checkSessionState();
    this.lift.style.bottom = `${this.current * 100}px`;
  },
  data() {
    return {
      callStack: [],
      vacant: true,
      current: 1,
      currentActive: 1,
      tableClass: '',
    }
  },
  computed: {
    lift() {
      return this.$refs.lift
    },
    localObjCopy() {
      return this.localObj
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
      localStorage.setItem(`currentActive${this.num}`, self.currentActive);
      this.vacant = false;
      let delta = Math.abs(id - this.current);
      // setting lift's position
      let interval = setInterval(function () {
        if (self.current > id) {
          self.tableClass = 'down';
          self.lift.style.bottom = `${parseFloat(self.lift.style.bottom) - 1}px`;
          if (self.lift.style.bottom <= `${id * 100}px`) {
            clearInterval(interval)
          }
        } else {
          self.tableClass = 'up';
          self.lift.style.bottom = `${parseFloat(self.lift.style.bottom) + 1}px`;
          if (self.lift.style.bottom >= `${id * 100}px`) {
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
        if (counter >= 3) {
          clearInterval(intervalFlashing);
          button.classList.remove('blue');
        }
      }, 500)
    },
    checkSessionState() {
      if (localStorage.getItem('current')) {
        this.current = localStorage.getItem('current');
      }
      if (localStorage.getItem('currentActive')) {
        this.currentActive = localStorage.getItem('currentActive');
      }
    }
  }
}
</script>

<style scoped lang="scss">
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

    &:first-child {
      opacity: 0;
    }
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
    width: 30px;
    border-top: 2px solid #6E18C0;
    border-right: 2px solid #6E18C0;

    &.down {
      transform: rotate(135deg);
    }

    &.up {
      transform: rotate(-45deg);
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