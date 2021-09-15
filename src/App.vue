<template>
	<div id="nav">Quiz Application</div>
	<div id="main">
		<h1>Quiz 1 - HTML/CSS/JS Practice</h1>
		<!-- results -->
		<results
			:amount="numberOfCorrectAnswers"
			:total="questionIds.length"
			:grade="usersGrade"
			v-if="isGradeCalc"
		></results>
		<!-- one question -->
		<div class="container" :key="questions.id" v-for="questions in allData">
			<question-container
				:id="questions.id"
				:class="checkAnswer(questions.id)"
				:header="questions.header"
				:answers="questions.answers"
				:answersToHighlight="correctedAnswers"
				@answerEvent="logAnswer"
			>
			</question-container>
			<div
				v-if="isGradeCalc"
				class="feedback-txt"
				:class="
					reviewedAnswers[questions.id] === 'correct' ? 'green-txt' : 'red-txt'
				"
			>
				<p>
					{{
						reviewedAnswers[questions.id] === "correct" ? "Correct" : "Wrong"
					}}
				</p>
			</div>
		</div>
		<!-- submit button -->
		<form @submit.prevent="submitAnswers">
			<div class="center">
				<button id="submit-btn" :disabled="isGradeCalc">Submit</button>
				<p class="red-txt" v-if="hasUnansweredQuestions">
					Answer all questions before submitting. Unanswered questions are
					displayed in yellow.
				</p>
			</div>
		</form>
	</div>
</template>

<script>
import json from "../data/questions.json";
import QuestionContainer from "./components/QuestionContainer.vue";
import Results from "./components/Results.vue";
export default {
	name: "App",
	components: {
		QuestionContainer,
		Results
	},
	data() {
		return {
			allData: json, // all data
			hasUnansweredQuestions: false,
			usersFinalAnswers: {},
			usersGrade: null,
			correctAnswerIds: [], //  array of correct answers (ABCD)
			correctAnswersText: [], //  array of correct answers (text)
			questionIds: json.map(x => x.id),
			isGradeCalc: false,
			numberOfCorrectAnswers: 0,
			reviewedAnswers: {}, // if answer is missing, incorrect, or correct based on question id
			correctedAnswers: [] // the correct answer for questions user got incorrect
		};
	},
	methods: {
		// adds classes based on missing, incorrect or correct answer
		checkAnswer(id) {
			if (this.reviewedAnswers[id] === "missing") {
				return "invalid";
			} else if (this.reviewedAnswers[id] === "incorrect") {
				return "incorrectAnswer";
			} else if (this.reviewedAnswers[id] === "correct") {
				return "correctAnswer";
			} else {
				return "valid";
			}
		},
		// takes v-model data from user input
		logAnswer(data) {
			let userData = Object.values(data);
			this.usersFinalAnswers[userData[0]] = userData[1];
		},
		// checks if all questions are answered
		submitAnswers() {
			let that = this;
			if (
				Object.keys(this.usersFinalAnswers).length === this.questionIds.length
			) {
				this.usersGrade = 0;
				this.hasUnansweredQuestions = false;
				this.calculateGrade();
			} else {
				let answeredQuestionIds = Object.keys(this.usersFinalAnswers);
				let allQuestions = Object.values(this.questionIds);
				let parsedIds = answeredQuestionIds.map(id => parseInt(id));
				allQuestions.forEach(currentId => {
					let idx = parsedIds.indexOf(currentId);
					if (idx !== -1) {
						that.reviewedAnswers[currentId] = ""; // taking away the yellow border for questions user fixed
					} else {
						that.hasUnansweredQuestions = true;
						that.reviewedAnswers[currentId] = "missing"; // sets question id to missing which will add yellow border
					}
				});
			}
		},
		// adds up users correct answers to calc grade, adds incorrect/correct borders
		calculateGrade() {
			this.correctAnswerIds = this.allData.map(x => x.correctAnswer.id);
			let userAnsArr = Object.entries(this.usersFinalAnswers); // users final answers (ABCD)
			for (let i = 0; i < userAnsArr.length; i++) {
				let questionIdOfAnswer = userAnsArr[i][0];
				let usersAnswer = userAnsArr[i][1];

				if (usersAnswer === this.correctAnswerIds[i]) {
					this.reviewedAnswers[questionIdOfAnswer] = "correct"; // compare answers, if they match set that questions id turn border green
					this.numberOfCorrectAnswers++;
				} else {
					this.reviewedAnswers[questionIdOfAnswer] = "incorrect"; // change incorrect questions id to turn border red
					this.highlightAnswer(questionIdOfAnswer);
				}
			}
			this.isGradeCalc = true;
			this.usersGrade = Math.floor(
				this.numberOfCorrectAnswers * (100 / this.questionIds.length) // get users final grade
			);
			return this.usersGrade;
		},
		highlightAnswer(id) {
			this.correctAnswersText = this.allData.map(
				data => data.correctAnswer.text
			); // array of the correct answers' text (not ABCD)
			if (this.reviewedAnswers[id] === "incorrect") {
				let answerToHighlight = this.correctAnswersText[id - 1]; // if the question is incorrect, take the correct answer and add it to correctedAnswers array to be higlighted green
				this.correctedAnswers.push(answerToHighlight);
			}
		}
	}
};
</script>

<style>
body {
	margin: 0;
	background-color: rgba(211, 211, 211, 0.4);
}
h1 {
	font-weight: 500;
	letter-spacing: 2px;
}
#app {
	font-family: Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
	margin: 60px;
}
#main {
	padding: 0 15%;
	text-align: left;
}
#nav {
	position: fixed;
	top: 0;
	left: 0;
	z-index: 1;
	width: 100%;
	height: 30px;
	background-color: darkred;
	color: white;
	text-align: left;
	padding: 15px 0 5px 15px;
	font-weight: 500;
}
#submit-btn {
	background-color: green;
	color: white;
	border: 2px solid black;
	border-radius: 2px;
	padding: 7px 12px;
}
.container {
	position: relative;
}
.red-txt {
	color: red;
}
.green-txt {
	color: green;
}
.center {
	text-align: center;
}
.invalid {
	border: 3px solid yellow;
}
.valid {
	border: 1px solid gray;
}
.correctAnswer {
	border: 3px solid green;
}
.incorrectAnswer {
	border: 3px solid red;
}
.feedback-txt {
	position: absolute;
	bottom: 0;
	right: 28px;
}
@media screen and (max-width: 768px) {
	#main {
		padding: 0 8% 0 2%;
	}
	#app {
		margin: 60px 14px;
	}
}
@media screen and (max-width: 375px) {
	#main {
		padding: 0;
	}
	h1 {
		font-size: 1.5rem;
	}
	.feedback-txt {
		bottom: -5px;
	}
}
</style>
