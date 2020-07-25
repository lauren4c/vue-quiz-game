<template>
<div class="question-box-container">
  <b-jumbotron>
  <template v-slot:lead>
  {{ currentQuestion.question }}
  </template>

  <hr class="my-4">
  <b-list-group>
    <b-list-group-item
    v-for="(answer, index) in answers"
    :key="index"
    @click="selectedAnswer(index)"
    :class="answerClass(index)"
    >
  {{ answer }}
  </b-list-group-item>
  </b-list-group>


  <b-button variant="primary"
@click="submitAnswer"
  :disabled="selectedIndex === null || answered"
  >
  Submit
</b-button>
  <b-button @click="next" variant="success" href="#">Next Question</b-button>
  </b-jumbotron>
</div>
</template>

<script>
import _ from 'lodash'

export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function
  },
  data() {
    return {
      selectedIndex: null,
      shuffledAnswers: [],
      correctIndex: null,
      answered: false,

    }
  },
  computed: {
    answers(){
    let answers = [...this.currentQuestion.incorrect_answers]
    answers.push(this.currentQuestion.correct_answer)
    return answers
    }
  },
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null
        this.answered = false
        this.shuffleAnswers()
      }
    }
  },
  methods: {
    selectedAnswer(index) {
      this.selectedIndex = index
    },
    submitAnswer() {
      let isCorrect = false
      if(this.selectedIndex === this.correctIndex){
        isCorrect = true
        console.log("this is correct")
      }
      this.answered = true
      this.increment(isCorrect)
    },
    shuffleAnswers() {
      let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]
      this.shuffledAnswers = _.shuffle(answers)
      this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
    },
    answerClass(index){
      let answerclass =''
      if (!this.answered && this.selectedIndex === index){
        answerclass="selected"
      }
      else if (this.answered && this.correctIndex === index ){
        answerclass="correct"
      }
      else if (this.answered && this.selectedIndex === index &&
        this.correctIndex !== index) {
        answerclass="incorrect"
      }
  return answerclass
    }
  }

}
</script>

<style scoped>
.list-group {
  margin-bottom: 15px;
}
.list-group-item:hover {
  background: #eee;
  cursor: pointer;
}
.btn{
  margin: 0 5px;
}

.selected {
  background-color: lightblue;
}

.correct {
  background-color:lightgreen;
}

.incorrect {
  background-color: red;
}
</style>
