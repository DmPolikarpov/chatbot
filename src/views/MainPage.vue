<script>
  import ChatWindow from '../components/chatwindow.vue'

  export default {
    name: 'MainPage',
    components: {
      ChatWindow
    },
    data() {
      return {
        botMessages: [
          {
            id: 0,
            message: "Привет! Что я могу для Вас сделать?",
            nextStep: 1
          },
          {
            id: 1,
            options: [
              {message: 'Заказать пиццу', nextStep: 2},
              {message: 'Установить будильник', nextStep: 3},
              {message: 'Вывести погоду', nextStep: 4},
            ]
          },
          {
            id: 2,
            message: "Хорошо, я закажу пиццу. Что еще могу сделать?",
            nextStep: 1
          },
          {
            id: 3,
            message: "Хорошо, я установлю будильник. Что еще могу сделать?",
            nextStep: 1
          },
          {
            id: 4,
            message: "Хорошо, я покажу погоду. Что еще могу сделать?",
            nextStep: 1
          },
          {
            id: 5,
            message: "Прошу прощения, но я такой команды еще не знаю. Что я могу для Вас сделать?",
            nextStep: 1,
            defOpt: true
          }
        ],
      }
    }
  }

</script>

<template>
  <ChatWindow :botMessages="botMessages" />
</template>
