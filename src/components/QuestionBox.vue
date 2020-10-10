<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template v-slot:lead>
        {{ currentQuestion.question }}
      </template>
      <hr class="my-4" />
      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in answers"
          :key="index"
          @click="selectAnswer(index)"
          :class="answerClass(index)"
        >
          {{ answer }}</b-list-group-item
        >
      </b-list-group>

      <b-button
        class="btn mx-2"
        variant="primary"
        @click="submitAnswer()"
        :disabled="selectedIndex === null || answered"
        >Submit</b-button
      >
      <b-button class="btn mx-2" variant="success" @click="next" href="#"
        >Next</b-button
      >
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";
export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function,
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false,
    };
  },
  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers];
      answers.push(this.currentQuestion.correct_answer);
      return answers;
    },
  },
  methods: {
    selectAnswer(index) {
      console.log(index);
      this.selectedIndex = index;
    },
    submitAnswer() {
      let isCorrect = false;
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }
      this.answered = true;
      this.increment(isCorrect);
    },
    shuffleAnswers() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer,
      ];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = this.shuffledAnswers.indexOf(
        this.currentQuestion.correct_answer
      );
    },
    answerClass(index) {
      let answeredClass = "";
      if (!this.answered && this.selectedIndex === index)
        answeredClass = "selected";
      else if (this.answered && this.correctIndex === index)
        answeredClass = "correct";
      else if (
        this.answered &&
        this.selectedIndex === index &&
        this.correctIndex !== index
      )
        answeredClass = "incorrect";

      return answeredClass;
    },
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.shuffleAnswers();
      },
    },
  },
  mounted() {
    console.log(this.currentQuestion);
    console.log();
  },
};
</script>

<style scoped>
.list-group {
  margin-bottom: 15px;
}
.list-group-item:hover {
  cursor: pointer;
  background: #eeeeee;
}
.answer-item:hover {
}
.selected {
  background-color: lightblue;
}
.correct {
  background-color: lightgreen;
}
.incorrect {
  background-color: lightsalmon;
}
</style>
