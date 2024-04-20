<script setup>
import { ref } from 'vue';

/* 
    1. this is a test question
        a. test 1a
        b. test 1b
        c. test 1c
    2. this is a test question 2
        a. test 2a
        b. test 2b
        c. test 2c
    3. this is a test question 3
        a. test 3a
        b. test 3a
        c. test 3a
    4. this is a test question 4
        a. test 4a
        b. test 4b
        c. test 4c
    5. this is a test question 5
        a. test 5a
        b. test 5b
        c. test 5c
*/

const inputQuestions = ref('');
const finishedQuestions = ref('');

const copyText = ref('Copy Text');

const parseQuestions = (v) => {
    return v.map((question) => {
        return `${question.question}\n${question.answers.map((answer) => {
            return `    ${answer}`;
        }).join('\n')}`;
    }).join('\n\n');
}

const shuffleQuestions = (type) => {
    //convert textarea string to an array of objects, where each question is an object
    let questionsObj = inputQuestions.value.split('\n').reduce((acc, line) => {
        if (line.trim() !== '') {
            if (line.trim().match(/^\d/)) {
                acc.push({
                    question: line,
                    answers: []
                });
            } else {
                if (acc.length > 0) {
                    acc[acc.length - 1].answers.push(line.trim());
                }
            }
        }
        return acc;
    }, []);

    if (type === 'q') {
        questionsObj = questionsObj.sort(() => Math.random() - 0.5);
        finishedQuestions.value = parseQuestions(questionsObj);

    } else if (type === 'a') {
        questionsObj = questionsObj.map((question) => {
            question.answers = question.answers.sort(() => Math.random() - 0.5);
            return question;
        });
        finishedQuestions.value = parseQuestions(questionsObj);

    } else if (type === 'qa') {
        questionsObj = questionsObj.sort(() => Math.random() - 0.5).map((question) => {
            question.answers = question.answers.sort(() => Math.random() - 0.5);
            return question;
        });
        finishedQuestions.value = parseQuestions(questionsObj);
    }
}

const copyQuestions = () => {
    navigator.clipboard.writeText(finishedQuestions.value);
    copyText.value = 'Copied!';
    setTimeout(() => {
        copyText.value = 'Copy Text';
    }, 2000);
}

</script>
<template>
    <div>
        <div class="main__container">

            <textarea v-model="inputQuestions" type="textarea" placeholder="Enter Questions here." rows="35"
                cols="50"></textarea>
            <textarea v-model="finishedQuestions" type="textarea" placeholder="Answers will show up here." rows="35"
                cols="50">
            </textarea>
            <button v-if="finishedQuestions" @click="copyQuestions" class="main__copy-button">{{ copyText }}</button>

        </div>
        <div class="main__btn-container">
            <button @click="shuffleQuestions('q')" :disabled="!inputQuestions">Shuffle Questions</button>
            <button @click="shuffleQuestions('a')" :disabled="!inputQuestions">Shuffle Answers</button>
            <button @click="shuffleQuestions('qa')" :disabled="!inputQuestions">Shuffle Questions and Answers</button>
        </div>
    </div>
</template>
<style scoped>
.main__container {
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 1rem;
    margin: 1rem 0;
    position: relative;

}

.main__container textarea {
    padding: 1rem;
    width: 50%;
    margin: 1rem 0;
    border: 2px solid var(--color-text);
    border-radius: 8px;
    box-sizing: border-box;
    color: #111;
    position: relative;

}

.main__container textarea:after {
    background-color: #111;
    border-radius: 8px;
    content: '';
    display: block;
    height: 48px;
    left: 0;
    width: 100%;
    position: absolute;
    top: -2px;
    transform: translate(8px, 8px);
    transition: transform 0.2s ease-out;
    z-index: -1;
}

.main__btn-container {
    display: flex;
    justify-content: flex-start;
    align-items: center;
    flex-wrap: wrap;
    gap: 2rem;
}

.main__copy-button {
    position: absolute;
    bottom: 3rem;
    right: 3rem;
}

@media (max-width: 1024px) {
    .main__container {
        flex-direction: column;
    }

    .main__container textarea {
        width: 100%;
    }
}
</style>