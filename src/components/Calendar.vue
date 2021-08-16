<template>
  <div class="calendar-wrapper">
    <div class="calendar-header" v-if="currentState === 'Days'">
      <button @click="decreaseMonth"><i class="fas fa-angle-left"></i></button>
      <b @click="currentState = 'Months'">
        {{ months[currentMonth] }} {{ currentYear }}
      </b>
      <button @click="increaseMonth"><i class="fas fa-angle-right"></i></button>
    </div>

    <div class="calendar-header" v-if="currentState === 'Months'">
      <button @click="currentYear--"><i class="fas fa-angle-left"></i></button>
      <b @click="currentState = 'Years'"> {{ currentYear }} </b>
      <button @click="currentYear++"><i class="fas fa-angle-right"></i></button>
    </div>

    <div class="calendar-header" v-if="currentState === 'Years'">
      <button @click="currentYear -= 10">
        <i class="fas fa-angle-left"></i>
      </button>
      <b>{{ currentYear - 2 }} - {{ currentYear + 9 }}</b>
      <button @click="currentYear += 10">
        <i class="fas fa-angle-right"></i>
      </button>
    </div>

    <div class="calendar-grid" v-if="currentState === 'Days'">
      <div class="calendar-weekdays">
        <div class="weekday" v-for="(weekday, index) in weekdays" :key="index">
          <b>{{ weekday }}</b>
        </div>
      </div>

      <div class="calendar-days">
        <div
          @click="selectDay(days.day, days.month)"
          :class="[
            { active: days.month === currentMonth },
            {
              activeDay: days.day === currentDay && days.month === currentMonth,
            },
            'day',
          ]"
          v-for="(days, index) in fillDaysArray"
          :key="index"
        >
          {{ days.day }}
        </div>
      </div>
    </div>

    <div class="calendar-months" v-if="currentState === 'Months'">
      <div
        @click="selectMonth(index)"
        class="month active"
        v-for="(month, index) in months"
        :key="index"
      >
        <b>{{ month }}</b>
      </div>
    </div>

    <div class="calendar-years" v-if="currentState === 'Years'">
      <div
        @click="selectYear(year)"
        class="year active"
        v-for="(year, index) in fillYearsArray"
        :key="index"
      >
        <b>{{ year }}</b>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      currentDay: new Date().getDate(),
      currentMonth: new Date().getMonth(),
      currentYear: new Date().getFullYear(),
      currentState: "Days",
      weekdays: ["Пн", "Вт", "Ср", "Чт", "Пт", "Сб", "Вс"],
      months: [
        "Январь",
        "Ферваль",
        "Март",
        "Апрель",
        "Май",
        "Июнь",
        "Июль",
        "Август",
        "Сентябрь",
        "Октябрь",
        "Ноябрь",
        "Декабрь",
      ],
    };
  },
  methods: {
    decreaseMonth() {
      if (!this.currentMonth) {
        this.currentMonth = 11;
        this.currentYear--;
      } else {
        this.currentMonth--;
      }
    },

    increaseMonth() {
      if (this.currentMonth === 11) {
        this.currentMonth = 0;
        this.currentYear++;
      } else {
        this.currentMonth++;
      }
    },

    selectDay(day, month) {
      if (month === this.currentMonth) {
        this.currentDay = day;
      } else if (month === "prevMonth") {
        this.decreaseMonth();
        this.currentDay = day;
      } else if (month === "nextMonth") {
        this.increaseMonth();
        this.currentDay = day;
      }
      console.log(this.currentDay);
    },

    selectMonth(month) {
      this.currentState = "Days";
      this.currentMonth = month;
    },

    selectYear(year) {
      this.currentState = "Months";
      this.currentYear = year;
    },

    getDaysInMonth(year, month) {
      return new Date(year, month + 1, 0).getDate();
    },

    getFirstDay(year, month) {
      let firstDay = new Date(year, month, 1).getDay() - 1;
      if (firstDay === -1) {
        return 6;
      } else {
        return firstDay;
      }
    },
  },
  computed: {
    fillDaysArray() {
      const firstDayOfMonth = this.getFirstDay(
        this.currentYear,
        this.currentMonth
      );
      const currentMonthDays = this.getDaysInMonth(
        this.currentYear,
        this.currentMonth
      );

      let daysArray = [];
      let previousMonthDays = this.getDaysInMonth(
        this.currentYear,
        this.currentMonth - 1
      );

      for (let i = firstDayOfMonth; i > 0; i--) {
        daysArray.push({ day: previousMonthDays, month: "prevMonth" });
        previousMonthDays--;
      }
      daysArray.reverse();

      for (let i = 1; i <= currentMonthDays; i++) {
        daysArray.push({ day: i, month: this.currentMonth });
      }

      let i = 1;
      while (daysArray.length < 42) {
        daysArray.push({ day: i, month: "nextMonth" });
        i++;
      }
      return daysArray;
    },

    fillYearsArray() {
      let yearsArray = [];
      let year = this.currentYear - 2;

      for (let i = 12; i > 0; i--) {
        yearsArray.push(year);
        year++;
      }
      return yearsArray;
    },
  },
};
</script>

<style lang="scss">
@import "https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.2/css/all.min.css";

.calendar-wrapper {
  padding: 20px;
  width: 350px;
  margin: auto;
  background: white;
  border-radius: 10px;
  border:1px solid gainsboro;
  user-select: none;
}

.calendar-header {
  margin: 10px 0;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  flex-wrap: wrap;
  color: black;

  b:hover {
    color: grey;
    cursor: pointer;
  }

  button {
    outline-style: none;
    background: none;
    height: 30px;
    border: none;
    cursor: pointer;
    font-size: 20px;

    &:hover {
      color: grey;
    }
  }
}

.day {
  border-radius: 10px;
  cursor: pointer;
  width: 30px;
  height: 30px;
  line-height: 30px;
  padding: 10px;
  color: darkgrey;
  -webkit-transition: all 0.1s;
  -moz-transition: all 0.1s;
  -o-transition: all 0.1s;
  transition: all 0.1s;

  &:hover {
    background: gainsboro;
  }
}

.active {
  color: black;
}
.activeDay {
  background: rgb(63, 94, 251);
  background: linear-gradient(
    90deg,
    rgba(63, 94, 251, 1) 0%,
    rgba(178, 70, 252, 1) 100%
  );
  color: white;
  &:hover {
    background: linear-gradient(
      90deg,
      rgba(63, 94, 251, 0.7) 0%,
      rgba(178, 70, 252, 0.7) 100%
    );
  }
}

.calendar-years,
.calendar-months,
.calendar-weekdays,
.calendar-days {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  flex-wrap: wrap;

  .year,
  .month {
    border-radius: 10px;
    cursor: pointer;
    width: 60px;
    height: 60px;
    padding: 20px;
    line-height: 60px;
    color: black;
    -webkit-transition: all 0.1s;
    -moz-transition: all 0.1s;
    -o-transition: all 0.1s;
    transition: all 0.1s;

    &:hover {
      background: gainsboro;
    }
  }
}

.weekday {
  width: 30px;
  height: 30px;
  padding: 10px;
  color: black;
  cursor: default;
}
</style>
