# explain.vuex.flow
Vuex の流れを理解する

## サンプルコード

### App.vue
```javascript
<template>
  <div id="app">
    <counter></counter>
  </div>
</template>

<script>
import Counter from "./components/Counter.vue";

export default {
  name: "app",
  components: {
    Counter,
  },
};
</script>
```

### components/Counter.vue
```javascript
<template>
  <div>
    <p :class="classesCount">{{ count }}</p>
    <button @click="sub">-</button>
    <button @click="add">+</button>
  </div>
</template>

<script>
import { mapState, mapGetters, mapMutations, mapActions } from "vuex";

export default {
  name: "Counter",
  computed: {
    ...mapState({
      count: (state) => state.counter.count,
    }),
    ...mapGetters({
      isPositive: "counter/isPositive",
      isNegative: "counter/isNegative",
    }),
    classesCount() {
      return {
        blue: this.isPositive,
        red: this.isNegative,
      };
    },
  },
  methods: {
    ...mapMutations({
      increment: "counter/increment",
      decrement: "counter/decrement",
    }),
    ...mapActions({
      addAsync: "counter/addAsync",
      subAsync: "counter/subAsync",
    }),
    add() {
      this.increment();
      this.addAsync({
        amount: 1000,
      });
    },
    sub() {
      this.decrement();
      this.subAsync({
        amount: 1000,
      });
    },
  },
};
</script>

<style scoped>
.red {
  color: red;
}
.blue {
  color: blue;
}
</style>
```

### modules/counter.js
```javascript
export const counter = {
    namespaced: true,
    state: {
        count: 0,
    },
    getters: {
        isPositive: state => {
            return state.count > 0
        },
        isNegative: state => {
            return state.count < 0
        },
    },
    mutations: {
        increment(state) {
            state.count++
        },
        decrement(state) {
            state.count--
        },
        add(state, amount) {
            state.count += amount
        },
        sub(state, amount) {
            state.count -= amount
        }
    },
    actions: {
        addAsync({ commit }, payload) {
            return new Promise((resolve, reject) => {
                setTimeout(() => {
                    commit('add', payload.amount)
                    resolve()
                }, 1000)
            })
        },
        subAsync({ commit }, payload) {
            return new Promise((resolve, reject) => {
                setTimeout(() => {
                    commit('sub', payload.amount)
                    resolve()
                }, 1000)
            })
        }
    }
}
```

### store.js
```javascript
import Vue from 'vue'
import Vuex from 'vuex'

import { counter } from './modules/counter';

Vue.use(Vuex)

export default new Vuex.Store({
    strict: process.env.NODE_ENV !== 'production',
    modules: {
        counter: counter
    }
})
```

## Vuex の全体図
!(Vuex の全体図)[./vuex.diagram.png]

## state を参照する
1. ストアの中に state を用意してコンポーネントの computed で参照する

!(state を参照する)[./ref_state_in_composents.vuex.png]

## Actions を dispatch する
2. methods の中なりライフサイクルの中なりから、 **Actions** を **dispatch** する

!(Actions を dispatch する)[./dispatch_actions.vuex.png]

