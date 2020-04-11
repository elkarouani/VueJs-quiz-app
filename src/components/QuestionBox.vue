<template>
    <div class="question-box-container">
        <b-jumbotron>
            <template slot="lead">
                {{ currentQuestion.question }}
            </template>

            <hr class="my-4">

            <b-list-group>
                <b-list-group-item 
                    v-for="(answer, index) in answers" 
                    :key="index" 
                    :class="answerClass(index)" 
                    @click="selectAnswer(index)"
                >
                    {{answer}}
                </b-list-group-item>
            </b-list-group>

            <b-button @click="submit" :disabled="selectedIndex === null || answered" variant="primary" href="#">Submit</b-button>
            <b-button @click="next" variant="success" href="#">Next</b-button>
        </b-jumbotron>
    </div>
</template>
<script>
    import _ from 'lodash';
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
                answered: false
            }
        },
        watch: {currentQuestion: {immediate: true, handler() {this.selectedIndex = null; this.shuffleAnswers(); this.answered = false;}}},
        methods: {
            selectAnswer: function(index) {this.selectedIndex = index;},
            shuffleAnswers: function() {
                this.shuffledAnswers = _.shuffle([...this.currentQuestion.incorrect_answers, this.currentQuestion.correct_answer]);
                this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer);
            },
            answerClass: function(index) {return !this.answered && this.selectedIndex === index ? 'selected' : this.answered && index == this.correctIndex ? 'correct' : this.answered && this.selectedIndex == index && index != this.correctIndex ? 'incorrect' : '';},
            submit: function() {this.increment((this.selectedIndex === this.correctIndex) ? true : false); this.answered = true;}
        },
        computed: {
            answers: function() {
                let answers = [...this.currentQuestion.incorrect_answers]
                answers.push(this.currentQuestion.correct_answer)
                return answers;
            } 
        }
    }
</script>
<style>
    .list-group {margin-bottom: 15px;}
    .list-group-item:hover {background-color: #EEE; cursor: pointer;}
    .btn {margin: 0 5px;}
    .selected {background-color: lightblue;}
    .correct {background-color: lightgreen;}
    .incorrect {background-color: lightcoral;}
</style>
