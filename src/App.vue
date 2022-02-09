<template>
  <div id="app">
    <div class="calendar">
      <div class="calendar__month-wrapper">
        <button class="month__toggler" @click="changeMonth(-1)">Prev</button>
        <p class="calendar__month">{{ monthList[setDate.month] }} {{ setDate.year }}</p>
        <button class="month__toggler" @click="changeMonth(1)">Next</button>
      </div>
      <div class="calendar__week-wrapper">
        <p class="calendar__week" v-for="day in weekList" :key="day">{{ day }}</p>
      </div>
      <div class="calendar__day-wrapper">
        <p
            :class="['calendar__day', {'day--active': day === setDate.day}]"
            v-for="day in daysInMonth"
            :key="day"
            @click="setDay(day)"
        >
          {{ day }}
        </p>
      </div>
    </div>

    <input type="text" class="date-input" @input="toggleInput" ref="dateInput">
    <p class="month-error" v-if="errorMonth === true">Месяц введен не корректно</p>

    <button class="change-language" @click="changeLang">Сменить язык</button>
  </div>
</template>

<script>


export default {
  name: 'App',
  data() {
    return {
      setDate: {                    // Установка даты
        year: 2022,
        month: 1,
        day: 1
      },
      lang: 'eng',                     // Язык
      errorMonth: false
    }
  },
  methods: {
    changeLang() {                          // Сменить язык
      if(this.lang === 'eng') {
        this.lang = 'rus';
      } else {
        this.lang = 'eng';
      }
    },
    changeMonth(side) {                             // Сменить месяц
      this.setDate.month += side;

      if(this.setDate.month === 12 && side === 1) {                 // Переключить год вперед, если перелистывается последний месяц
        this.setDate.month = 0;
        this.setDate.year += 1;
      } else if(this.setDate.month < 0 && side === -1) {            // Переключить год назад, если перелистывается первый месяц
        this.setDate.month = 11;
        this.setDate.year -= 1;
      }

    },
    setDay(day) {                                                 // Функция для смены дня через календарь
      this.setDate.day = day;

      this.returnDate();                                          // Обновить дату
    },
    returnDate() {                                                                                                              // Функция для обновления даты
      this.$refs.dateInput.value = this.setDate.day + ' ' + this.monthList[this.setDate.month] + ' ' + this.setDate.year;        // Записать дату в инпут
    },
    setDefaultDate() {                                                            // Фунция для установки нынешней даты
      this.setDate.year = this.currentYear;
      this.setDate.month = this.currentMonth;
      this.setDate.day = this.currentDay;

      this.$refs.dateInput.value = this.setDate.day + ' ' + this.monthList[this.setDate.month] + ' ' + this.setDate.year;         // Записать дату в инпут
    },
    toggleInput() {                                                                                 // Функция для обработки изменений в инпуте
      let val = this.$refs.dateInput.value;

      let valArray = val.split(' ');                                                // Разбить скроку из инпута на массив

      this.setDate.year = Number(valArray[2]);
      this.setDate.day = Number(valArray[0]);

      if(this.monthList.includes(valArray[1])) {                              // Если введен верный месяц - сменить значение месяца
        let number = this.monthList.indexOf(valArray[1]);                     // Получить индекс набранного месяца
        this.setDate.month = number;
        this.errorMonth = false;                                              // Если месяц введен не верно - вывести ошибку
        console.log(this.errorMonth)
      } else {
        this.errorMonth = true;                                              // Если месяц введен верно - убрать ошибку
      }

    }
  },
  computed: {
    monthList() {
      if(this.lang === 'rus') {
        return [
          'Январь',
          'Февраль',
          'Март',
          'Апрель',
          'Май',
          'Июнь',
          'Июль',
          'Август',
          'Сентябрь',
          'Октябрь',
          'Ноябрь',
          'Декабрь'
        ]
      }

      return [
        'January',
        'February',
        'March',
        'April',
        'May',
        'June',
        'July',
        'August',
        'September',
        'October',
        'November',
        'December'
      ]
    },              // Список месяцев на разных языках
    weekList() {
      if(this.lang === 'rus') {
        return [
          'Вск',
          'Пон',
          'Вт',
          'Ср',
          'Чт',
          'Пн',
          'Сб'
        ]
      }
      return [
        'Sun',
        'Mon',
        'Tue',
        'Wed',
        'Thu',
        'Fri',
        'Sat'
      ]
    },                // Список недель на разных языках
    daysInMonth() {
      const date1 = new Date(this.setDate.year, this.setDate.month, this.setDate.day);
      const date2 = new Date(this.setDate.year, this.setDate.month + 1, this.setDate.day);
      const date3 = Math.round((date2 - date1) / 1000 / 3600 / 24);

      return date3;
    },              // Сколько дней в месяце
    currentYear() {
      return new Date().getFullYear();
    },               // Текущий год
    currentMonth() {
      return new Date().getMonth();
    },              // Текущий месяц
    currentDay() {
      return new Date().getDate();
    }                 // Текущий день
  },
  watch: {
    lang() {                            // При смене языка напускаееется функция с возвратом даты (для смены языка в input)
      this.returnDate();
    }
  },
  mounted() {                           // При открытии приложения ставится сегодняшняя дата
    this.setDefaultDate();
  }
}
</script>

<style src="./assets/style.css"></style>
