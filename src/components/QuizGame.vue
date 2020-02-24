<template>
  <div class="game">
    <div class="game-board">
      <div>
        <div class="question">
          <div class="operand">
            {{ question.firstOperand }}
          </div>
          <div class="operator">
            {{ operator }}
          </div>
          <div class="operand">
            {{ question.secondOperand }}
          </div>
        </div>
        <div class="answer">
          <div v-if="question.correct === null" class="computed-answer">
            {{ computedAnswer }}
          </div>
          <div class="user-answer">{{ userAnswer ? userAnswer : "?" }}</div>
        </div>
        <div class="level">Level: {{ level }} Points: {{ points }}</div>
      </div>
    </div>
    <div>
      <div class="debug">
        High Score Level: {{ highScoreLevel }} High Score Time:
        {{ highScoreTime }} Start Time: {{ startTime }}
      </div>
      <div v-for="(q, i) in unfinishedQuestions" :key="i" class="debug">
        {{ q }}
      </div>
      <div v-for="(q, i) in incorrectQuestions" :key="i" class="debug">
        {{ q }}
      </div>
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
import { Howl } from "howler";

function generateQuestions(level) {
  let questions = [];

  for (let i = 1; i <= level; i++) {
    for (let j = i; j <= level; j++) {
      if (Math.random() < 0.5) {
        questions.push({ firstOperand: i, secondOperand: j, correct: false });
      } else {
        questions.push({ firstOperand: j, secondOperand: i, correct: false });
      }
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
    userAnswer: null,

    level: 1,
    points: 0,

    questionNumber: null,
    questions: [],
    numberOfQuestions: null,
    incorrectQuestions: [],

    startTime: null,
    highScoreLevel: 1,
    highScoreTime: null,

    operator: "+",
    showKeypad: true,
    maxLength: 6,

    correctSound: new Howl({ src: ["correct.mp3"] }),
    incorrectSound: new Howl({ src: ["incorrect.mp3"] })
  }),

  computed: {
    question: function() {
      if (this.questionNumber != null) {
        return this.unfinishedQuestions[this.questionNumber];
      }
      return { firstOperand: 0, secondOperand: 0, correct: false };
    },

    unfinishedQuestions: function() {
      return this.questions.filter(question => !question.correct);
    },

    computedAnswer: function() {
      return this.question.firstOperand + this.question.secondOperand;
    }
  },

  watch: {
    points: function() {
      if (this.points < 0) {
        this.points = 0;
        this.level--;
        return;
      }

      if (
        this.incorrectQuestions.length >
        Math.log2(this.numberOfQuestions) + 1
      ) {
        this.level--;
      }
    },

    level: function() {
      if (this.level < 1) {
        this.level = 1;
      }

      this.levelUp(); // Or down, depending.
    }
  },

  created: function() {
    this.levelUp();
  },

  methods: {
    levelUp() {
      if (this.level > this.highScoreLevel) {
        this.highScoreLevel = this.level;
        this.highScoreTime = Date.now() - this.startTime;
      }
      this.questions = generateQuestions(this.level);
      this.numberOfQuestions = this.questions.length;
      this.incorrectQuestions = [];
      this.nextQuestion();
    },

    nextQuestion() {
      this.userAnswer = null;

      let doneNew = this.questions.every(question => question.correct == true);

      const doneReDo = this.incorrectQuestions.length == 0;

      if (doneNew && !doneReDo) {
        this.questions = this.incorrectQuestions;
        this.incorrectQuestions = [];
        doneNew = false;
      }

      if (!doneNew) {
        this.questionNumber = Math.floor(
          Math.random() * this.unfinishedQuestions.length
        );
      } else {
        this.level++;
      }
    },

    onInput(key) {
      const userInput = key == 11 ? 0 : key;

      if (!this.userAnswer) {
        this.userAnswer = "";
      }
      this.userAnswer = (this.userAnswer + userInput).slice(0, this.maxLength);
    },

    onSubmit() {
      if (!this.userAnswer) return;

      if (this.level === 1 && this.points === 0) {
        this.startTime = Date.now();
      }

      if (
        this.userAnswer ==
        this.question.firstOperand + this.question.secondOperand
      ) {
        this.correctSound.play();

        this.points++;

        this.question.correct = true;
        this.nextQuestion();
      } else {
        this.incorrectSound.play();

        this.points--;

        this.question.correct = null;
        this.userAnswer = null;

        if (this.level != 1) {
          this.incorrectQuestions.push({
            firstOperand: this.question.firstOperand,
            secondOperand: this.question.secondOperand,
            correct: false
          });
        }
      }
    },

    onReset() {
      this.userAnswer = null;
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.debug {
  font-size: 10px;
  /* visibility: hidden;*/
}

.game {
  font-size: 56px;
  max-width: 700px;
  margin: 0 auto;
}

.game-board {
  height: 200px;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.question {
  width: 2em;
  display: grid;
  grid-gap: 10px;
  grid-template-columns: repeat(3, auto);
}

.answer {
  text-align: center;
}

.user-answer,
.computed-answer {
  display: inline-block;
  width: 50%;
}

.user-answer {
  color: cornflowerblue;
}

.computed-answer {
  color: coral;
  opacity: 0.3;
}

.level {
  font-size: 1rem;
  color: #888;
}

.question,
.answer,
.level {
  width: 50%;
  text-align: center;
  margin: 10px auto 0 auto;
}
</style>
