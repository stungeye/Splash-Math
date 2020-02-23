<template>
  <div :class="'keypad-dialog ' + animation">
    <div class="keypad-container">
      <template v-for="n in 12">
        <div :key="n" class="keypad-flex keypad-class">
          <button v-if="n == 10 && onReset" class="keypad" @click="onReset">
            <div class="keypad-bigger">❌</div>
          </button>
          <button
            v-if="n != 10 && n != 12"
            class="keypad"
            :ripple="true"
            @click="onInput(n)"
          >
            <div v-if="n < 10">{{ n }}</div>
            <div v-if="n == 11">0</div>
          </button>
          <button
            v-if="n == 12 && onSubmit"
            class="keypad"
            @click="onSubmit(n)"
          >
            <div class="keypad-bigger">✔️</div>
          </button>
        </div>
      </template>
    </div>
  </div>
</template>

<script>
export default {
  name: "KeyPad",
  props: {
    keypadClass: { type: String, default: "keypad-class", required: false },
    show: { type: Boolean, default: true, required: false },
    close: { type: String, default: "Close", required: false },
    onInput: { type: Function, required: true },
    onSubmit: { type: Function, required: false, default: function() {} },
    onDelete: { type: Function, required: false, default: function() {} },
    onReset: { type: Function, required: false, default: function() {} }
  },
  data: () => ({
    n: 0,
    animation: "keypad-hide"
  }),
  watch: {
    show() {
      this.animation === "slideInUp"
        ? (this.animation = "slideOutDown")
        : (this.animation = "slideInUp");
    }
  },
  mounted() {
    this.show ? (this.animation = "slideInUp") : (this.animation = "hide");
  },
  methods: {
    closeMe() {
      this.animation = "slideOutDown";
    }
  }
};
</script>

<style>
.keypad-hide {
  visibility: hidden;
}

.keypad-class {
  color: #888;
  background: #fafafa;
  border: 0.004rem solid #eaeaea;
}

.keypad-dialog {
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
}

.keypad-container {
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  -webkit-box-flex: 1;
  -ms-flex: 1 1 auto;
  flex: 1 1 auto;
  -ms-flex-wrap: wrap;
  flex-wrap: wrap;
  min-width: 0;
  flex-direction: row;
}

.keypad-value {
  width: 100%;
  text-align: right;
  padding: 0.5rem;
}

.keypad-flex {
  flex-basis: 33%;
  -webkit-box-flex: 0;
  -ms-flex-positive: 0;
  flex-grow: 0;
  max-width: 33%;
  min-height: 4rem;
}

.keypad {
  width: 100%;
  height: 115%;
  text-align: center;
  vertical-align: center;
  font-size: 1.3rem;
  background-color: #fafafa;
  border: none;
}

button.keypad:focus,
button.keypad:active {
  outline: none;
}

button.keypad::-moz-focus-inner {
  border: 0;
}

button.keypad:active {
  background-color: #ccc;
}

button.keypad div {
  line-height: 60px;
}

.keypad-bigger {
  font-size: 1.5rem;
}

.slideInUp {
  -webkit-animation-name: slideInUp;
  animation-name: slideInUp;
  -webkit-animation-duration: 1s;
  animation-duration: 1s;
  -webkit-animation-fill-mode: both;
  animation-fill-mode: both;
}
@-webkit-keyframes slideInUp {
  0% {
    -webkit-transform: translateY(100%);
    transform: translateY(100%);
    visibility: visible;
  }
  100% {
    -webkit-transform: translateY(0);
    transform: translateY(0);
  }
}
@keyframes slideInUp {
  0% {
    -webkit-transform: translateY(100%);
    transform: translateY(100%);
    visibility: visible;
  }
  100% {
    -webkit-transform: translateY(0);
    transform: translateY(0);
  }
}

.slideOutDown {
  -webkit-animation-name: slideOutDown;
  animation-name: slideOutDown;
  -webkit-animation-duration: 1s;
  animation-duration: 1s;
  -webkit-animation-fill-mode: both;
  animation-fill-mode: both;
}
@-webkit-keyframes slideOutDown {
  0% {
    -webkit-transform: translateY(0);
    transform: translateY(0);
  }
  100% {
    visibility: hidden;
    -webkit-transform: translateY(100%);
    transform: translateY(100%);
  }
}
@keyframes slideOutDown {
  0% {
    -webkit-transform: translateY(0);
    transform: translateY(0);
  }
  100% {
    visibility: hidden;
    -webkit-transform: translateY(100%);
    transform: translateY(100%);
  }
}
</style>
