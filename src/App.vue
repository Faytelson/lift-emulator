<template>
  <div id="app">
    <div class="container-xl">
      <LiftComponent
          v-for="lift in lifts"
          :stages="stages"
          :key="lift.id"
          :ref="lift.id"
          @createLocal="createLocalObj"
          @sendClearCallstack="clearCallStackItem"
          @sendCheckStack="checkStack"
      ></LiftComponent>
      <div class="buttons">
        <div
            class="buttons__button-container"
            v-for="button in stages"
            :key="button.id"
        >
          <button
              class="buttons__button"
              @click="checkActive(button.id, $event.target)"
          >Call lift
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import LiftComponent from "@/components/LiftComponent";

export default {
  name: 'App',
  components: {
    LiftComponent
  },
  mounted() {
    this.checkSessionState();
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
      lifts: [{id: 1}, {id: 2}, {id: 3}],
      callStack: [],
    }
  },
  methods: {
    checkSessionState() {
      let liftsArr = JSON.parse(localStorage.getItem('localArr'));
      if(liftsArr) {
        let lifts = this.$refs;
        let liftNodes = document.querySelectorAll('.lift-track__lift');
        for(let key in lifts) {
          lifts[key][0].current = liftsArr[key - 1].current;
          lifts[key][0].currentActive = liftsArr[key - 1].currentActive;
          liftNodes[key - 1].style.bottom = `${lifts[key][0].current * 100}px`;
        }
      } else {
        this.createLocalObj();
      }
    },
    createLocalObj() {
      let liftsArr = [];
      let lifts = this.$refs;
      for(let key in lifts) {
        let liftObj = {
          id: Number(key),
          current: lifts[key][0].current,
          currentActive: lifts[key][0].currentActive,
          vacant: lifts[key][0].vacant,
        }
        liftsArr.push(liftObj)
      }
      localStorage.setItem('localArr', JSON.stringify(liftsArr));
    },
    checkActive(buttonId, button) {
      if (!this.callStack.includes(buttonId)) {
        this.callStack.push(buttonId)
      }
      let liftsArr = JSON.parse(localStorage.getItem('localArr'));
      if(liftsArr) {
        let currentValues = [];
        let filtered = liftsArr.filter(lift => ((buttonId !== lift.currentActive) && lift.vacant));
        if(filtered.length > 0) {
          filtered.forEach(lift => currentValues.push(Math.abs(buttonId - lift.current)));
          let active = Math.min(...currentValues);
          let filteredActive = filtered.find(lift => Math.abs(buttonId - lift.current) === active);
          let lifts = this.$refs;
          lifts[filteredActive.id][0].launchLift(buttonId, button);
        }
      }
    },
    checkStack() {
      const buttons = document.querySelectorAll('.buttons__button');
      if (this.callStack.length > 0) {
        this.checkActive(this.callStack[0], buttons[this.callStack[0]]);
      }
    },
    clearCallStackItem() {
      this.callStack.shift(this.callStack[0]);
    },
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

.container-xl {
  display: flex;
  gap: 50px;
}

.buttons {
  width: 50px;
  display: flex;
  flex-direction: column-reverse;

  &__button-container {
    width: 100%;
    height: 100px;
    display: flex;
    align-items: center;
    justify-content: center;

    &:first-child {
      opacity: 0;
    }
  }

  &__button {
    width: 50px;
    height: 50px;
    border-radius: 50%;
    cursor: pointer;
    border: 1px solid #000;
  }

  .blue {
    background-color: #DAF0FF !important;
  }
}
</style>
