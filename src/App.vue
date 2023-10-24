<template>
	<main class="app">
		<h1>🦾 CapEscape Quiz 🦾</h1>
		<section class="loading" v-if="!quizStarted">
			<p>I see you found the tiny ducklings! For this round, you will be answering
			<span style="color: rgb(240, 230, 140);">5 tech related questions.</span></p>
			<br>
			<p>In order for you to retrieve the number for your lock, you will need to pass
			<span style="color: rgb(240, 230, 140);">AT LEAST 3 questions</span>
			. For each question, you have
			<span style="color: rgb(240, 230, 140);">only one shot at it. </span>So only select IF YOU ARE SURE!</p>
			<br>
			<p>Don't worry, you can re-do the whole quiz, but time is not your friend 🙃 So, are you ready to start?</p>
			<div class="bbox">
				<button @click="startQuiz">Let's GO!</button>
			</div>
		</section>

		<section class="quiz" v-if="!quizCompleted" v-show="quizStarted">
			<div class="quiz-info">
				<span class="question">{{ getCurrentQuestion.question }}</span>
				<span class="score">Score {{ score }}/{{ questions.length }}</span>
			</div>

			<div class="options">
				<label v-for="(option, index) in getCurrentQuestion.options" :for="'option' + index" :class="`option ${getCurrentQuestion.selected == index
					? index == getCurrentQuestion.answer
						? 'correct'
						: 'wrong'
					: ''
				} ${getCurrentQuestion.selected != null &&
					index != getCurrentQuestion.selected
					? 'disabled'
					: ''
				}`">
					<input type="radio" :id="'option' + index" :name="getCurrentQuestion.index" :value="index"
						v-model="getCurrentQuestion.selected" :disabled="getCurrentQuestion.selected"
						@change="setAnswer" />
					<span>{{ option }}</span>
				</label>

				<div class="bbox">
					<button @click="nextQuestion" :disabled="!getCurrentQuestion.selected">
						{{
						getCurrentQuestion.index == questions.length - 1
						? 'Finish'
						: getCurrentQuestion.selected == null
						? 'Select an option'
						: 'Next question'
						}}
					</button>
				</div>

			</div>


		</section>

		<section v-else>
			<h2>You made it to the end!</h2>
			<h2>Your score is {{ score }}/{{ questions.length }}</h2>
			<h2 class ="pwd" v-if="score >= 3">D = 2 😉</h2>
			<div class="bbox">
				<h2 class ="pwd" v-if="score < 3">Too bad, let's try again 😵‍💫</h2>
				<button v-if="score < 3" @click="resetQuiz">Restart</button>
			</div>
		</section>
	</main>
</template>

<script setup>
import { ref, computed } from 'vue'

const questions = ref([
  {
	question: "1) Which of the following programming languages is primarily associated with the development of artificial intelligence and machine learning applications?",
	answer: 1,
	options: [
		'Java',
		'Python',
		'C++',
		'Ruby'
	],
	selected: null
  },
   {
	   question: "2) What is the name of the British computer scientist who invented the World Wide Web in 1989?",
		answer: 0,
		options: [
			'Tim Berners-Lee',
			'Alan Turing',
			'Vint Cerf',
			'Larry Page'
		],
		selected: null
	},
	{
		question: "3) What is the name of the oldest programming language still in use?",
		answer: 2,
		options: [
			'Matlab',
			'Ada',
			'FORTRAN',
			'COBOL'
		],
		selected: null
	},
	{
		question: "4) What kind of malware is designed to take advantage of a security hole before it is known?",
		answer: 0,
		options: [
			'Zero-day exploit',
			'Virus',
			'Ransomware',
			'Trojan Horse'
		],
		selected: null
	},
	{
		question: "5) Which was the first internet search engine in the world?",
		answer: 3,
		options: [
			'Google',
			'Bing',
			'Yahoo',
			'Archie'
		],
		selected: null
	},
])

const quizStarted = ref(false)
const quizCompleted = ref(false)
const currentQuestion = ref(0)
const score = computed(() => {
	let value = 0
	questions.value.map(q => {
		if (q.selected != null && q.answer == q.selected) {
			value++
		}
	})
	return value
})

const getCurrentQuestion = computed(() => {
	let question = questions.value[currentQuestion.value]
	question.index = currentQuestion.value
	return question
})

const setAnswer = (e) => {
	questions.value[currentQuestion.value].selected = e.target.value
	e.target.value = null
}

const nextQuestion = () => {
	if (currentQuestion.value < questions.value.length - 1) {
		currentQuestion.value++
		return
	}

	quizCompleted.value = true
}

const startQuiz = () => {
	quizStarted.value = true
}

const resetQuiz = () => {
	quizStarted.value = false
	quizCompleted.value = false;
	currentQuestion.value = 0;
	questions.value.map((q) => {
		q.selected = null
	})
}
</script>


<style>
@import url('https://fonts.googleapis.com/css2?family=Inconsolata:wght@300;400&display=swap');
* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
	font-family: 'Inconsolata', monospace;
}

body {
	background-color: #2E8BC0;
	color: #FFF;
}

.app {
	display: flex;
	flex-direction: column;
	align-items: center;
	padding: 2rem;
	height: 100vh;
}

h1 {
	font-size: 2.5rem;
	margin-bottom: 2rem;
}

h2 {
	font-size: 2rem;
	margin-bottom: 2rem;
	text-align: center;
}

h3 {
	font-size: 1.5rem;
	margin-top: 0.5rem;
	text-align: center;
}

p {
	color: #fff;
	font-size: 1rem;
	text-align: center;
}

.pwd {
	color: rgb(240, 230, 140);
	font-size: 2rem;
	text-align: center;
}

.loading {
	background-color: #145DA0;
    padding: 1rem;
    width: 100%;
    max-width: 640px;
}

.loading p {
    font-size: 1.5rem;
    margin: 2px;
}

.quiz {
	background-color: #145DA0;
	padding: 1rem;
	width: 100%;
	max-width: 640px;
}

.quiz-info {
	display: flex;
	justify-content: space-between;
	margin-bottom: 1rem;
}

.quiz-info .question {
	color: #FFF;
	font-size: 1.25rem;
}

.score {
	margin:1px;
	border-radius: 12px;
	border: 1px solid #FFF;
	text-align:center;
	padding:5px;
}

.options {
	margin-bottom: 1rem;
}

.option {
	padding: 1rem;
	display: block;
	background-color: #0C2D48;
	margin-bottom: 0.5rem;
	border-radius: 0.5rem;
	cursor: pointer;
}

.option:hover {
	background-color: #60A3D9;
}

.option.correct {
	background-color: #2cce7d;
}

.option.wrong {
	background-color: #ff5a5f;
}

.option:last-of-type {
	margin-bottom: 0;
}

.option.disabled {
	opacity: 0.5;
}

.option input {
	display: none;
}

.bbox {
	display:flex;
	justify-content:center;
	flex-direction: column;
	align-items: center;
}

button {
	outline: none;
	border: none;
	cursor: pointer;
	padding: 1rem;
	margin: 1.5rem;
	background-color: #2cce7d;
	color: #2d213f;
	font-weight: 700;
	text-transform: uppercase;
	font-size: 1.2rem;
	border-radius: 0.5rem;
}

button:disabled {
	opacity: 0.5;
}

.logo {
	max-width: 10em;
}

</style>
