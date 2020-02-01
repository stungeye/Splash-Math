<template>
  <div class="game">
    <div class="question">
      <div class="operand">
        5
      </div>
      <div class="operator">
        +
      </div>
      <div class="operand">
        4
      </div>
    </div>
    <div>Answer: {{ number }}</div>
    <keypad
      :on-input="onInput"
      :on-submit="onSubmit"
      :on-reset="onReset"
      :show="showKeypad"
    />
  </div>
</template>

<script>
import keypad from "@/components/KeyPad.vue";

export default {
  name: "QuizGame",
  components: {
    keypad
  },
  data: () => ({
    number: "",
    maxLength: 6,
    showKeypad: true
  }),
  methods: {
    onInput(key) {
      this.number = (this.number + key).slice(0, this.maxLength);
    },
    onSubmit() {
      alert("Submitted: " + this.number);
    },
    onReset() {
      this.number = "";
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.game {
  font-size: 56px;
}
.question {
  display: grid;
  grid-gap: 10px;
  grid-template-columns: repeat(3, auto);
}
</style>
