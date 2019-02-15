<template>
  <div class="game container">
    <div class="game__question item flex-basis-700 container column">
      <div class="game__question__text">
        {{ question.question }}
      </div>
      <div class="game__question__words container wrap justify-content-around">
        <span
          v-for="a in question.answers"
          :draggable="!a.selected && !question.answered"
          :class="{ disabled: a.selected }"
          @dragstart="dragstart(a)"
          @dragend="dragend(a)"
        >
            {{ a.word }}
        </span>
      </div>
      <div class="game__question__info">
        <div class="game__question__info__title">
          <p>Make the Sentence</p>
        </div>
        <button class="game__question__info__check" type="button" name="button" :disabled="!filled" @click="answerQuestion()">
          CHECK
        </button>
      </div>
      <div class="game__question__sentence container justify-content-center">
        <span
          v-for="(a, ind) in answers"
          :draggable="a.selected && !question.answered"
          :class="{ empty: !a.selected }"
          @dragenter="dragenter(a)"
          @dragleave="dragleave(a)"
          @dragover="$event.preventDefault()"
          @drop="drop(a)"
          @dragstart="dragstart(a)"
          @dragend="dragend(a)"
        >
          {{ a.selected ? a.selected.word : `Word ${++ind}` }}
        </span>
      </div>
    </div>
    <div class="game__answer item flex-basis-400 container column">
      <div class="game__answer__img">
        <img :src="question.img" alt="Question Image">
      </div>
      <div class="game__answer__result container align-items-center yours">
        <div class="game__answer__result__title container align-items-center yours">
          Your Sentence
        </div>
        <div class="game__answer__result__text container justify-content-start align-items-center">
          <span
            v-for="a in answers"
            :class="{ wrong: (question.answered && (!a.selected || (a.selected.order == null) || (a.selected.order !== a.correct.order))) }"
          >
            {{ !a.selected ? '' : a.selected.word }}&nbsp
          </span>
        </div>
      </div>
      <div class="game__answer__result container align-items-center correct">
        <div class="game__answer__result__title container align-items-center correct">
          Correct Sentence
        </div>
        <div class="game__answer__result__text container justify-content-start align-items-center">
          {{ question.answered ? question.text : '' }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { cloneDeep } from 'lodash'

export default {
  name: 'app',
  data () {
    return {
      sentence: '',
      toDrop: null,
      questions: [],
      question: {
        img: 'https://www.worldatlas.com/r/w728-h425-c728x425/upload/4b/18/e8/tv-television-watching.jpg',
        question: 'Observe a imagem e logo apos ouça os audios e selecione aquele que melhor corresponde à imagem.',
        text: 'He is watching TV',
        answered: false,
        shuffled: false,
        answers: [{
          word: 'She',
          selected: false
        },
        {
          word: 'Me',
          selected: false
        },
        {
          word: 'are',
          selected: false
        },
        {
          word: 'was',
          selected: false
        },
        {
          word: 'were',
          selected: false
        },
        {
          word: 'eating',
          selected: false
        },
        {
          word: 'looking',
          selected: false
        },
        {
          word: 'drinking',
          selected: false
        },
        {
          word: 'an',
          selected: false
        },
        {
          word: 'on',
          selected: false
        },
        {
          word: 'in',
          selected: false
        },
        {
          word: 'leafs',
          selected: false
        },
        {
          word: 'chips',
          selected: false
        },
        {
          word: 'soda',
          selected: false
        }]
      }
    }
  },
  computed: {
    textWords () {
      return this.question.text.replace(/[^a-zA-Z0-9' ]/g, '').split(' ')
    },
    filled () {
      for (const a of this.answers) {
        if (!a.selected) return false
      }
      return true
    },
    answers: {
      cache: false,
      get () {
        let aswr_words = this.textWords.map((w, index) => ({ word: w, order: index })),
            words = this.question.answers.filter(w => w.selected)

        aswr_words = aswr_words.map(w => ({ correct: w, selected: null}))

        for (const w of words) {
          this.$set(aswr_words[w.position], 'selected', w)
        }

        return aswr_words
      }
    }
  },
  methods: {
    setWords () {
      let words = this.question.answers

      if (!this.question.shuffled) {
        const aswr_words = this.textWords.map((w, index) => ({ word: w, order: index, selected: false }))
        words = [ ...words, ...aswr_words ]
        this.$set(this.question, 'shuffled', true)
      }
      this.$set(this.question, 'answers', this.shuffle(words))
    },
    shuffle (arr) {
      let lgth = arr.length;
      let temp;
      let index;

      while (lgth > 0) {
        index = Math.floor(Math.random() * lgth)
        lgth--

        temp = arr[lgth]
        arr[lgth] = arr[index]
        arr[index] = temp
      }
      return arr;
    },
    dragstart (word) {
      this.toDrop = word
    },
    dragend (word) {
      this.toDrop = null
    },
    dragenter (word) {
      this.canDrop = true
    },
    dragleave (word) {
      this.canDrop = false
    },
    drop (word) {
      if (this.canDrop && !this.question.answered) {
        let toDrop = this.toDrop

        if (typeof toDrop.selected === 'boolean') {
          if (word.selected) {
            this.$set(word.selected, 'selected', false)
            this.$delete(word.selected, 'position')
          }
          toDrop.selected = true
          toDrop.position = word.correct.order
          this.canDrop = false
        } else {
          if (word.correct.order !== toDrop.correct.order) {
            const index = this.question.answers.findIndex(a => a.word == toDrop.selected.word)
            let selected = cloneDeep(this.question.answers[index])
            this.$set(selected, 'position', word.correct.order)
            this.$set(this.question.answers, index, selected)
            if (word.selected) {
              const temp_index = this.question.answers.findIndex(a => a.word == word.selected.word)
              let temp = cloneDeep(this.question.answers[temp_index])
              this.$set(temp, 'position', toDrop.correct.order)
              this.$set(this.question.answers, temp_index, temp)
            }
            this.$nextTick()
          }
        }
      }
    },
    answerQuestion () {
      if (!this.question.answered) {
        this.$set(this.question, 'answered', true)
      }
    }
  },
  mounted () {
    this.setWords()
  }
}
</script>

<style lang="scss">
.container {
  display: flex;
  &.column {
    flex-direction: column;
  }
  &.wrap {
    flex-wrap: wrap;
  }
  &.justify-content-start {
    justify-content: flex-start;
  }
  &.justify-content-center {
    justify-content: center;
  }
  &.justify-content-around {
    justify-content: space-around;
  }
  &.align-items-center {
    align-items: center;
  }
}

.item {
  flex-grow: 1;
}

.game {
  padding: 20px;
  min-height: 400px;
  max-width: 1050px;
  &__question {
    flex-basis: 680px;
    margin-right: 20px;
    &__text {
      border: 2px solid #DDD;
      border-radius: 15px;
      padding: 10px 15px;
      margin-bottom: 10px;
    }
    &__words {
      border: 2px solid #DDD;
      border-radius: 15px;
      padding: 10px 15px;
      margin-bottom: 10px;
      span {
        border-radius: 25px;
        padding: 5px 15px;
        background-color: yellow;
        margin: 5px;
        cursor: not-allowed;
        &.disabled {
          background-color: #DDD;
        }
        &[draggable="true"] {
          cursor: pointer;
        }
      }
    }
    &__info {
      margin-bottom: 10px;
      display: flex;
      justify-content: space-between;
      &__title {
        p {
          padding: 10px 25px;
          background-color: #DDD;
          border-radius: 25px;
          margin: 0;
        }
      }
      &__check {
        cursor: pointer;
        outline: none;
        border: none;
        border-radius: 25px;
        padding: 10px 25px;
        background-color: rgb(93, 230, 164);
        color: #FFF;
        &:disabled {
          cursor: not-allowed;
          background-color: #DDD;
          color: #AAA;
        }
      }
    }
    &__sentence {
      border: 2px solid #DDD;
      border-radius: 15px;
      padding: 10px 15px;
      span {
        border-radius: 25px;
        padding: 5px 15px;
        background-color: yellow;
        margin: 5px;
        cursor: not-allowed;
        &.empty {
          min-width: 68px;
          min-height: 18px;
          background-color: #DDD;
          text-align: center;
        }
        &[draggable="true"] {
          cursor: pointer;
        }
      }
    }
  }
  &__answer {
    flex-basis: 500px;
    &__img {
      img {
        max-width: 100%;
        max-height: 100%;
      }
    }
    &__result {
      border-radius: 10px;
      margin-top: 10px;
      padding-right: 15px;
      height: 50px;
      &.correct {
        background-color: rgb(156, 218, 153);
        color: rgb(33, 77, 30);
      }
      &.yours {
        background-color: rgb(144, 178, 224);
        color: rgb(46, 75, 113);
        span {
          &.wrong {
            color: rgb(139, 22, 23);
          }
        }
      }
      &__title {
        border-radius: 10px;
        margin-right: 10px;
        padding: 5px 10px;
        flex-basis: 65px;
        text-align: center;
        height: calc(100% - 5px);
        color: #FFF;
        &.correct {
          background-color: rgb(33, 77, 30);
        }
        &.yours {
          background-color: rgb(46, 75, 113);
        }
      }
      &__text {
        padding: 5px 0px;
        flex-grow: 1;
        height: 100%;
      }
    }
  }
}

</style>
