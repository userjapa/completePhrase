<template>
  <div class="game container">
    <div class="game__question item flex-basis-700 container column">
      <div class="game__question__text">
        {{ question.question }}
      </div>
      <div class="game__question__words container wrap">
        <span v-for="a in question.answers">{{ a.word }}</span>
      </div>
      <div class="game__question__sentence">
        <span v-for="a in answers"></span>
      </div>
    </div>
    <div class="game__answer item flex-basis-400 container column">
      <div class="game__answer__img">
        <img src="https://i.ytimg.com/vi/zU5OjNkMOHk/hqdefault.jpg" alt="Question Image">
      </div>
      <div class="game__answer__result">
        {{ result }}
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
      result: '',
      answers: [],
      questions: [],
      question: {
        question: 'Observe a imagem e logo apos ouça os audios e selecione aquele que melhor corresponde à imagem.',
        text: 'He is roling a joint',
        answers: [{
          word: 'He',
          order: 1,
        },
        {
          word: 'She'
        },
        {
          word: 'Me'
        },
        {
          word: 'is',
          order: 2,
        },
        {
          word: 'are'
        },
        {
          word: 'was'
        },
        {
          word: 'were'
        },
        {
          word: 'rolling',
          order: 3
        },
        {
          word: 'smoking'
        },
        {
          word: 'looking'
        },
        {
          word: 'a',
          order: 4
        },
        {
          word: 'an'
        },
        {
          word: 'on'
        },
        {
          word: 'in'
        },
        {
          word: 'joint',
          order: 5
        },
        {
          word: 'leafs'
        },
        {
          word: 'this'
        }]
      }
    }
  },
  computed: {
    textWords () {
      return cloneDeep(this.question.text.replace(/[^a-zA-Z0-9' ]/g, '').split(' '))
    }
  },
  mounted () {
    this.answers = this.textWords
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
}

.item {
  border: 2px solid red;
  flex-grow: 1;
  &.flex-basis-700 {
    flex-basis: 700px;
  }
  &.flex-basis-400 {
    flex-basis: 400px;
  }
}

.game {
  &__question {
    padding: 5px 10px;
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
        margin-bottom: 10px;
        &:not(:last-child) {
          margin-left: 10px;
        }
      }
    }
    &__sentence {
      border: 2px solid #DDD;
      border-radius: 15px;
      padding: 10px 15px;
    }
  }
  &__answer {

  }
}

</style>
