<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template slot="lead">
        {{ currentQuestion.question }}
      </template>

      <hr class="my-4">

      <b-list-group>
        <b-list-group-item
          class="my-1"
          v-for="(answer, index) in shuffledAnswers"
          :key="index"
          @click="selectAnswer(index)"
          :class="answerClass(index)"
        >
          {{ answer }}
        </b-list-group-item>        
      </b-list-group>

      <b-button
       class="m-2"
       variant="primary"
       @click="submitAnswer"
       :disabled="selectedIndex === null || answered"
      >
       Submit
      </b-button>
      <b-button class="m-2" @click="next" variant="success">Next</b-button>
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
        correctIndex: null,
        shuffledAnswers: [],
        answered: false
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
      selectAnswer(index) {
        this.selectedIndex = index
      },
      submitAnswer() {
        let isCorrect = false

        if(this.selectedIndex === this.correctIndex){
          isCorrect = true
        }
        this.answered = true

        this.increment(isCorrect)
      },
      shuffleAnswers() {
        let answers = [...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]

        this.shuffledAnswers = _.shuffle(answers)

        this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
      },
      answerClass(index) {
        let answerClass = ''

        if(!this.answered && this.selectedIndex === index){
          answerClass = 'selected'
        } else if(this.answered && this.correctIndex === index) {
          answerClass = 'correct'
        } else if(this.answered 
          && this.selectedIndex === index 
          && this.correctIndex !== index 
        ) {
          answerClass = 'incorrect'
        }

        return answerClass
      }
    }
  }
</script>

<style>
  .list-group-item:hover {
    background: cyan;
    cursor: pointer;
  }

  .selected {
    background-color: lightblue;
  }

  .correct {
    background-color: lightgreen;
  }

  .incorrect {
    background-color: lightcoral;
  }
</style>
