<template>
  <div id="app">
    <div class="container-xl">
      <LiftComponent
          v-for="lift in lifts"
          :stages="stages"
          :key="lift.id"
          :active="lift.id === active"
          :currentBtn="currentBtn"
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
      active: null,
      currentValues: [],
      currentBtn: null,
    }
  },
  methods: {
    checkActive(buttonId, button) {
      let liftsArr = JSON.parse(localStorage.getItem('localArr'));
      if(liftsArr) {
        liftsArr.forEach(lift => {
          this.currentValues.push(Math.abs(buttonId - lift.current));
          this.active = Math.min(...this.currentValues);
        })
      } else {
        this.active = 1;
      }
      this.currentBtn = {
        target: button,
        id: buttonId,
      };
      console.log('active lift id is: ', this.active)
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
}
</style>
