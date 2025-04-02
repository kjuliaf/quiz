<script lang="ts">
	import { quiz } from './quiz';

	// Set initial values
	let score = 0;
	let currentIndex = 0;
	let selectedAnswers: ({ answer: string; isCorrect: boolean } | null)[] = Array(quiz.length).fill(null);
	let showResult = false;

	// Get current question dynamically
	$: currentQuestion = quiz[currentIndex];

	function handleAnswerSelection(answer: { answer: string; isCorrect: boolean }) {
		// Reset score on change
		if (selectedAnswers[currentIndex]?.isCorrect) {
			score--; // Undo previous correct selection
		}
		// Set new answer and score
		selectedAnswers[currentIndex] = answer;
		if (answer.isCorrect) {
			score++;
		}
	}

	function handleNext() {
		if (currentIndex < quiz.length - 1) {
			currentIndex++;
		} else {
			showResult = true;
		}
	}

	function handlePrevious() {
		if (currentIndex > 0) {
			currentIndex--;
		}
	}

	function handleRetake() {
		// Reset values
		score = 0;
		currentIndex = 0;
		selectedAnswers.fill(null);
		showResult = false;
	}
</script>

<div class="flex h-screen">
	<div class="m-auto flex flex-col items-center gap-8 py-40">
		{#if !showResult}
		
			<!-- Show quiz -->
			<fieldset class="flex flex-col items-center gap-6">

				<!-- Question -->
				<legend class="mb-6 px-6 text-center text-xl">
					<span class="text-xl font-bold">Question {currentIndex + 1}:</span>
					{currentQuestion.question}
				</legend>
				<div>

					<!-- Answers -->
					{#each currentQuestion.answers as quizAnswer, i}
						<div class="flex gap-3 p-0.5">
							<input
								type="radio"
								id={quizAnswer.answer}
								name="answer"
								class="radio radio-primary"
								on:change={() => handleAnswerSelection(quizAnswer)}
								checked={selectedAnswers[currentIndex]?.answer === quizAnswer.answer}
							/>
							<label for={quizAnswer.answer}>{quizAnswer.answer}</label>
						</div>
					{/each}
				</div>

				<!-- Previous and Next buttons -->
				<div class="flex gap-2">
					{#if currentIndex > 0}
						<button class="btn btn-outline btn-primary" on:click={handlePrevious}>Previous</button>
					{/if}
					<button class="btn btn-primary" disabled={!selectedAnswers[currentIndex]} on:click={handleNext}>
						Next
					</button>
				</div>
			</fieldset>

			<!-- Progression -->
			<ul class="steps">
				{#each quiz, i}
					<li
						data-content={i < currentIndex ? '✓' : i == currentIndex && selectedAnswers[currentIndex] ? '✓' : ''}
						class="step font-bold {i <= currentIndex ? 'step-primary' : ''}"
					>
						{i + 1}
					</li>
				{/each}
			</ul>
		{:else}

			<!-- Result -->
			<p class="text-lg">Your score is <span class="text-primary text-xl font-bold">{score}/{quiz.length}</span></p>
			<button class="btn btn-primary" on:click={handleRetake}>Retake</button>
		{/if}
	</div>
</div>
