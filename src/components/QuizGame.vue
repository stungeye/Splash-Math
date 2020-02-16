<template>
  <div class="game">
    <div class="game-board">
      <div>
        <div class="question">
          <div class="operand">
            {{ question.first_operand }}
          </div>
          <div class="operator">
            {{ operator }}
          </div>
          <div class="operand">
            {{ question.second_operand }}
          </div>
        </div>
        <div class="answer">{{ number }}</div>
        <div class="level">Level: {{ level }} Points: {{ points }}</div>
      </div>
    </div>
    <div>
      <div class="debug" v-for="q in unfinishedQuestions" v-bind:key="q">
        {{ q }}
      </div>
      <div class="debug">Question Number: {{ questionNumber }}</div>
    </div>
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

function generate_questions(level) {
  let questions = [];
  for (let i = 1; i <= level; i++) {
    for (let j = 1; j <= level; j++) {
      questions.push({ first_operand: i, second_operand: j, correct: false });
    }
  }
  return questions;
}

export default {
  name: "QuizGame",
  components: {
    keypad
  },
  data: () => ({
    number: "?",
    maxLength: 6,
    showKeypad: true,
    level: 1,
    points: 0,
    operator: "+",
    questionNumber: null,
    questions: []
  }),

  computed: {
    question: function() {
      if (this.questionNumber != null) {
        return this.unfinishedQuestions[this.questionNumber];
      }
      return { first_operand: "?", second_operand: "?" };
    },
    unfinishedQuestions: function() {
      return this.questions.filter(question => !question.correct);
    }
  },

  created: function() {
    this.levelUp();
  },

  methods: {
    levelUp() {
      this.questions = generate_questions(this.level);
      this.nextQuestion();
    },
    nextQuestion() {
      this.number = "?";
      const doneLevel = this.questions.every(
        question => question.correct == true
      );
      if (!doneLevel) {
        this.questionNumber = Math.floor(
          Math.random() * this.unfinishedQuestions.length
        );
      } else {
        this.level++;
        this.levelUp();
      }
    },
    onInput(key) {
      const number = key == 11 ? 0 : key;
      if (this.number == "?") {
        this.number = "";
      }
      this.number = (this.number + number).slice(0, this.maxLength);
    },
    onSubmit() {
      if (
        this.number ==
        this.question.first_operand + this.question.second_operand
      ) {
        this.question.correct = true;
        this.points++;
      } else {
        this.points--;
        console.log("wrong");
        if (this.points < 0) {
          console.log("level down");
          this.points = 0;
          this.level--;
          if (this.level < 1) {
            this.level = 1;
          }
          this.levelUp();
        }
      }
      this.nextQuestion();
    },
    onReset() {
      this.number = "?";
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.debug {
  font-size: 10px;
  visibility: hidden;
}
.game {
  font-size: 56px;
}
.game-board {
  height: 200px;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.question {
  display: grid;
  grid-gap: 10px;
  grid-template-columns: repeat(3, auto);
}

.answer {
  color: cornflowerblue;
}

.level {
  font-size: 1rem;
  color: #888;
}

.question,
.answer,
.level {
  text-align: center;
  width: 90%;
  margin: 10px auto 0 auto;
}
</style>
