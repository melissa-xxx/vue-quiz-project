<template>
	<!-- one question -->
	<div class="questionBox" :id="id">
		<p>{{ id }}. {{ header }}</p>
		<!-- answers -->
		<div
			v-for="answer in answers"
			:key="answer"
			:id="answer.text"
			class="answerBox"
			:class="{ highlightedAnswer: answersToHighlight.includes(answer.text) }"
		>
			<input
				type="radio"
				:value="[id, answer.answerId]"
				:name="id"
				:id="[id, answer.answerId]"
				v-model="userInput"
				@change="trigger"
			/>
			<label :for="[id, answer.answerId]">{{ answer.text }}</label>
		</div>
	</div>
</template>

<script>
export default {
	props: ["id", "header", "answers", "answersToHighlight"],
	emits: ["answerEvent"],
	data() {
		return {
			userInput: []
		};
	},
	methods: {
		trigger() {
			this.$emit("answerEvent", this.userInput);
		}
	}
};
</script>

<style>
.questionBox {
	text-align: left;
	padding: 20px;
	border: 1px solid rgba(128, 128, 128, 0.4);
	border-bottom: 1px solid rgba(128, 128, 128);
	border-radius: 9px;
	background-color: white;
	margin-bottom: 20px;
}
.answerBox {
	margin-left: 40px;
	margin-bottom: 20px;
}
.highlightedAnswer {
	background-color: green;
	color: white;
	border: 1px solid black;
	padding: 10px;
	border-radius: 5px;
}
@media screen and (max-width: 375px) {
	.answerBox {
		margin-left: 0;
	}
}
</style>
