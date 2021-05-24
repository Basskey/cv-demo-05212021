<template>
  <div class="layout">
    <h1 class="title">cv-demo-05212021</h1>

    <form class="form" @submit.prevent="onSubmit">
      <label class="form-input-label" for="versionList">Place your numbers here:</label>
      <div class="form-container">
        <input class="form-input" id="versionList" type="text" />
        <button class="form-submit-btn" type="submit">Which newest?</button>
      </div>
    </form>

    <span class="result-field">Newest is: {{ result }}</span>
  </div>
</template>

<script>


export default {
  name: 'App',

  data() {
    return {
      result: '...'
    }
  },

  methods: {
    onSubmit() {

      const incomingData = document.querySelector('input').value //забрали строку из инпута вида '10.0.1, 5.2, 14.0.1.1, 14.0.0'
          .split(',') //разбили её на массивы по запятой и получили ['10.0.1', ' 5.2', ' 14.0.1.1', ' 14.0.0']
          .map(item => item.trim() //убрали ненужные пробелы и т.п. в каждом элементе - ['10.0.1', '5.2', '14.0.1.1', '14.0.0']
              .split('.')  //каждый элемент массива разбили на подмассив разбив по точкам [['10','0','1'], ..., ['14','0','0']]
              .map(string => +string) //элементы подмассива преобразуем из строк в числа, получая двухэтажный массив, готовый для обработки
          )                                 //такого вида: [[10,0,1],[5,2],[14,0,1,1],[14,0,0]]

      //проверяем какой длины нам пришли подмассивы: [3, 2, 4, 3]
      let calcDataLength = incomingData.map(item => item.length)

      //выбираем самый длинный (4), что бы использовать как лимит итератора в compareNumeric()
      let longestVersion = Math.max.apply(Math, calcDataLength);
      //это покрывает кейс, когда сравниваются версии типа 1.1.0 и 1.1.0.1, без проверки результат будет некорректный,
      //давая циклу понять сколько именно итераций нам нужно для корректной сортировки на этапе сравнения минорных версий, задавая "глубину погружения"
      //я мог бы сделать по хорошему еще какую нибудь валидацию, для покрытия кейсов с вводом неформатной белиберды, но можно ненадо? :3

      function compareNumeric(a, b) {
        let i = 0

        //сравниваем по очереди во всех подмассивах версии начиная с наиболее мажорных, итеративно продвигаясь к более минорным
        while (i < longestVersion) {
            if (a[i] > b[i]) return -1;
            if (a[i] < b[i]) return 1;
            if (a[i] === b[i] && i === longestVersion) return 0;
            i++
        }
      }

      //сортируем массив который получили из инпута, упорядочивая версии по убыванию
      incomingData.sort(compareNumeric)

      //самый первый элемент после сортировки и будет искомой наисвежайшей версией из списка
      //преобразуем его обратно в строку для красоты и выведем через интерполяцию в шаблоне страницы
      this.result = incomingData[0].join('.')
    }
  }
}
</script>

<style lang="scss">
@import 'assets/styles'; //я тут кусок своей готовой палитры и типографики для быстроты добавил если вы не против

  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }

  html, body {
    width: 100%;
    height: 100%;
  }

  body {
    background-color: $basic-dark-blue;
  }

  .layout {
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: center;
    min-height: 100%;
    max-width: 400px;
    margin: 0 auto;
    padding: 10px;
    color: #90B443;
  }

  .title {
    margin: 1em auto;
  }

  .form {
    width: 100%;
    margin: 0 auto 20px;

    &-container {
      display: flex;
      justify-content: space-between;
      width: 100%;
    }

    &-input {
      border: none;
      border-radius: 5px;
      flex-grow: 1;
      margin: 0 10px 0 0;
      outline: none;
      padding: 0 10px;
    }

    &-input-label {
      margin-bottom: 5px;
      display: block;
    }

    &-submit-btn {
      border: none;
      border-radius: 5px;
      padding: 10px;
      background-color: $secondary-green;
      color: $white;
      &:hover {
        background-color: $primary-green;
      }
      &:active {
        background-color: $auxiliary-green;
      }
    }
  }

  .result-field {
    width: 100%;
  }
</style>
