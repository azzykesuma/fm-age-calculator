<script setup lang="ts">
  import { ref } from 'vue';
  interface formObject {
    day : number;
    month: number;
    year: number
  }
  interface resultYear {
    years: number | string;
    months: number | string;
    days: number | string;
  }

  interface errorTextFormat {
    day: string;
    month: string;
    year: String
  }
  const errorText = ref<errorTextFormat>({
    day: '',
    month: '',
    year: ''
  })

  const hasError = ref({
    dayError :  false,
    monthError :  false,
    yearError :  false
  })

  const currentDate = new Date();
  const dateObject = ref<formObject>({
    day: currentDate.getDate(),
    month: currentDate.getMonth() + 1,
    year: new Date().getFullYear()
  });


  const resultUserYear = ref<resultYear>({
    years: 0,
    months: 0,
    days: 0
  })

  function isLeapYear(year: number): boolean {
    return (year % 4 === 0 && year % 100 !== 0) || year % 400 === 0;
  }

function getDaysInMonth(year: number, month: number): number {
  const daysInMonth = [
    31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31
  ];
  if (month === 2 && isLeapYear(year)) {
    return 29;
  }
    return daysInMonth[month - 1];
  }

  function validateForm():boolean {
  //  null check
    let isValid = true
    if(!dateObject.value.day) {
      isValid = false;
      errorText.value.day = 'This fields is required'
      hasError.value.dayError = true
    }
    if(!dateObject.value.month) {
      isValid = false;
      errorText.value.month = 'This fields is required'
      hasError.value.monthError = true
    }
    if(!dateObject.value.year) {
      isValid = false;
      errorText.value.year = 'This fields is required'
      hasError.value.yearError = true
    }
  // if the year is not in the past
  const currentYear = new Date().getFullYear();

  if (
  dateObject.value.year &&
  (dateObject.value.year > currentYear ||
    (dateObject.value.year === currentYear &&
      (dateObject.value.month > currentDate.getMonth() + 1 ||
        (dateObject.value.month === currentDate.getMonth() + 1 &&
          dateObject.value.day > currentDate.getDate()))))
  ) {
    isValid = false;
    hasError.value.yearError = true;
    errorText.value.year = 'Must be in the past';
  }
  if (dateObject.value.month && (dateObject.value.month < 1 || dateObject.value.month > 12)) {
    isValid = false;
    errorText.value.month = 'Must be a valid month';
    hasError.value.monthError = true;
  }
  if (dateObject.value.day && (dateObject.value.day < 1 || dateObject.value.day > 31)) {
    isValid = false;
    errorText.value.day = 'Must be a valid day';
    hasError.value.dayError = true;
  } else if (dateObject.value.month && dateObject.value.year) {
    const maxDays = getDaysInMonth(dateObject.value.year, dateObject.value.month);
    if (dateObject.value.day && (dateObject.value.day < 1 || dateObject.value.day > maxDays)) {
      isValid = false;
      errorText.value.day = 'Must be a valid day';
      hasError.value.dayError = true;
    }
  }
    return isValid
  }
  const submitForm = (e:Event) => {
    e.preventDefault();
    if(validateForm()) {
      // removing all errors
      hasError.value.dayError = false
      hasError.value.monthError = false
      hasError.value.yearError = false
      errorText.value.day = ''
      errorText.value.month = ''
      errorText.value.year = ''

      const inputDate = new Date(
        dateObject.value.year,
        dateObject.value.month - 1,
        dateObject.value.day
      );
  
      const ageInMilliseconds = currentDate.getTime() - inputDate.getTime();
      const ageDate = new Date(ageInMilliseconds);
      resultUserYear.value.years = ageDate.getUTCFullYear() - 1970;
      resultUserYear.value.months = ageDate.getUTCMonth();
      resultUserYear.value.days = ageDate.getUTCDate() - 1;
    } else {
      resultUserYear.value.years = '--'
      resultUserYear.value.days = '--'
      resultUserYear.value.months = '--'
    }
  }
</script>

<template>
  <main>
    <section class="card_counter">
    <form @submit="submitForm" action="#">
      <div class="form-container">
        <div class="form-item">
          <label for="day">DAY</label>
          <input
          type="text"
          name="day"
          placeholder="DD"
          :class="{'error' : hasError.dayError}"
          v-model="dateObject.day"/>
          <p>{{ errorText.day }}</p>
        </div>
        <div class="form-item">
          <label for="month">MONTH</label>
          <input :class="{'error' : hasError.monthError}" placeholder="MM" type="text" name="month" v-model="dateObject.month" />
          <p>{{ errorText.month }}</p>
        </div>
        <div class="form-item">
          <label for="year">YEAR</label>
          <input :class="{'error' : hasError.yearError}" placeholder="YYYY" type="text" name="year" v-model="dateObject.year" />
          <p>{{ errorText.year }}</p>
        </div>
      </div>

      <hr>
      <button type="submit">
        <img src="./assets/images/icon-arrow.svg" alt="icon-arrow-button">
      </button>
    </form>
    </section>
    <section class="result-counter">
      <h2><span>{{ resultUserYear.years }}</span> years</h2>
      <h2><span>{{ resultUserYear.months }}</span> months</h2>
      <h2>
        <span>
          {{ resultUserYear.years }}
        </span> days</h2>
    </section>
  </main>
</template>

