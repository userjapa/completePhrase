<template>
  <div class="game container">
    <div class="game__question item flex-basis-700 container column">
      <div class="game__question__text">
        {{ question.question }}
      </div>
      <div class="game__question__words container wrap justify-content-around">
        <span
          v-for="a in words"
          :draggable="!a.selected"
          :class="{ disabled: a.selected }"
          @dragstart="dragstart(a, $event.target)"
          @dragend="dragend(a, $event.target)"
        >
            {{ a.word }}
        </span>
      </div>
      <div class="game__question__info">
        <div class="game__question__info__title">
          <p>Make the Sentence</p>
        </div>
        <button class="game__question__info__check" type="button" name="button" :disabled="!filled" @click="check()">
          CHECK
        </button>
      </div>
      <div class="game__question__sentence container justify-content-center">
        <span
          v-for="(a, ind) in answers"
          :class="{ empty: !a.selected }"
          @dragenter="dragenter(a, $event.target)"
          @dragleave="dragleave(a, $event.target)"
          @dragover="$event.preventDefault()"
          @drop="drop(a, $event.target)"
        >
          {{ a.selected ? a.selected.word : `Word ${++ind}` }}
        </span>
      </div>
    </div>
    <div class="game__answer item flex-basis-400 container column">
      <div class="game__answer__img">
        <img src="https://www.worldatlas.com/r/w728-h425-c728x425/upload/4b/18/e8/tv-television-watching.jpg" alt="Question Image">
      </div>
      <div class="game__answer__result container align-items-center yours">
        <div class="game__answer__result__title container align-items-center yours">
          Your Sentence
        </div>
        <div class="game__answer__result__text container justify-content-start align-items-center">
          <span v-for="a in answers" :class="{ wrong: (checked && (!a.selected || (a.selected.order == null) || (a.selected.order !== a.correct.order))) }">{{ a.selected ? a.selected.word : '' }}&nbsp</span>
        </div>
      </div>
      <div class="game__answer__result container align-items-center correct">
        <div class="game__answer__result__title container align-items-center correct">
          Correct Sentence
        </div>
        <div class="game__answer__result__text container justify-content-start align-items-center">
          {{ checked ? question.text : '' }}
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
      answers: [],
      words: [],
      toDrop: null,
      checked: false,
      questions: [],
      question: {
        question: 'Observe a imagem e logo apos ouça os audios e selecione aquele que melhor corresponde à imagem.',
        text: 'He is watching TV',
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
      return cloneDeep(this.question.text.replace(/[^a-zA-Z0-9' ]/g, '').split(' '))
    },
    filled () {
      for (const a of this.answers) {
        if (!a.selected) return false
      }
      return true
    }
  },
  methods: {
    setWords () {
      const aswr_words = this.textWords.map((w, index) => ({ word: w, order: index, selected: false })),
            words = [...this.question.answers, ...aswr_words]

      this.answers = cloneDeep(aswr_words).map(w => ({ correct: w, selected: null }))
      this.words = this.shuffle(words)
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
    dragstart (word, target) {
      console.log('Drag Start', word, target)
      this.toDrop = word
    },
    dragend (word, target) {
      console.log('Drag End', word, target)
      this.toDrop = null
    },
    dragenter (word, target) {
      console.log('Drag Enter', word, target)
      this.canDrop = true
    },
    dragleave (word, target) {
      console.log('Drag Leave', word, target)
      this.canDrop = false
    },
    drop (word, target) {
      console.log('Drop', word, target)
      word.selected = this.toDrop
      this.toDrop.selected = true
      this.canDrop = false
    },
    check () {
      this.checked = true
      console.log(this.answers)
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
        cursor: pointer;
        border-radius: 25px;
        padding: 5px 15px;
        background-color: yellow;
        margin: 5px;
        &.disabled {
          cursor: not-allowed;
          background-color: #DDD;
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
        &.empty {
          min-width: 68px;
          min-height: 18px;
          background-color: #DDD;
          text-align: center;
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
