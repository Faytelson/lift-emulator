<template>
  <div id="app">
    <div class="container-xl">
      <LiftComponent
          :stages="stages"
          :localObj="localObj"
          v-for="lift in lifts"
          :key=lift.id
          :num="lift.id"
          :ref='`lift${lift.id}`'
      ></LiftComponent>
      <div class="buttons">
        <div
            class="buttons__button-container"
            v-for="button in stages"
            :key="button.id"
        >
          <button
              class="buttons__button"
              @click="checkLifts"
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
    this.setLocalObject();
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
      localObj: {}
    }
  },
  computed: {
    getLocalObj() {
      return JSON.stringify(this.localObj)
    }
  },
  methods: {
    checkLifts() {

    },
    setLocalObject() {
      localStorage.setItem('localObj', this.getLocalObj);
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
}
</style>
