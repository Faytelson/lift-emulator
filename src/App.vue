<template>
  <div id="app">
    <div class="container-xl">
      <LiftComponent
          v-for="lift in lifts"
          :stages="stages"
          :key="lift.id"
          :currentBtn="currentBtn"
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
    this.createLocalObj()
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
      currentBtn: null,
      callStack: [],
    }
  },
  methods: {
    checkActive(buttonId, button) {
      if (!this.callStack.includes(buttonId)) {
        this.callStack.push(buttonId)
      }
      let liftsArr = JSON.parse(localStorage.getItem('localArr'));
      console.log('liftsArr', liftsArr)
      if(liftsArr) {
        let currentValues = [];
        let filtered = liftsArr.filter(lift => ((buttonId !== lift.currentActive) && lift.vacant));
        console.log('filtered', filtered)
        if(filtered.length > 0) {
          filtered.forEach(lift => currentValues.push(Math.abs(buttonId - lift.current)));
          let active = Math.min(...currentValues);
          let filteredActive = filtered.find(lift => Math.abs(buttonId - lift.current) === active);
          let lifts = this.$refs;
          for(let key in lifts) {
            if(Number(key) === Number(filteredActive.id)) {
              lifts[key][0].launchLift(buttonId, button);
            }
          }
        }
      }
      this.currentBtn = {
        target: button,
        id: buttonId,
      };
    },
    createLocalObj() {
      // let liftsArr = JSON.parse(localStorage.getItem('localArr'));
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
        localStorage.setItem('localArr', JSON.stringify(liftsArr))
    },
    checkStack() {
      const buttons = document.querySelectorAll('.buttons__button');
      if (this.callStack.length > 0) {
        this.checkActive(this.callStack[0], buttons[this.callStack[0]]);
      }
    },
    clearCallStackItem() {
      this.callStack.shift(this.callStack[0]);
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
