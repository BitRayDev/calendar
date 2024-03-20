<template>
  <div class="calendar">
    <div class="calendar__grid">
      <div class="calendar__month-selector month-selector">
        <img class="month-selector__arrow month-selector__arrow_left" alt="left-arrow"
             src="https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Right_arrow.svg/434px-Right_arrow.svg.png"
             @click="selectPreviousMonth"/>
        <span class="month-selector__selected-month">{{ selectedMonthLabel }}</span>
        <img class="month-selector__arrow month-selector__arrow_right" alt="right-arrow"
             src="https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Right_arrow.svg/434px-Right_arrow.svg.png"
             @click="selectNextMonth"/>
      </div>
      <span class="calendar__weekday-label" v-for="label in weekdaysLabels" :key="label">{{ label }}</span>
      <span class="calendar__date" :class="{'calendar__date_selected': isDaySelected(day)}"
            v-for="day in daysInObservingMonth" :key="day" :style="getDateStyle(day)" @click="selectDay(day)">{{ day }}</span>
    </div>
  </div>
</template>

<script setup>
import {computed, onMounted, ref} from "vue";

const props = defineProps({
  locale: {
    type: String,
    default: 'en-US'
  }
})
const selectedDate = defineModel({type: Date, default: () => new Date()})

const weekdaysLabels = computed(() => {
  const labels = [];
  for (let i = 0; i < 7; i++) {
    const weekday = new Intl.DateTimeFormat(props.locale, {
      weekday: "short"
    }).format(new Date(2001, 0, i));
    labels.push(weekday);
  }
  return labels;
})
const observingMonthDate = ref(selectedDate.value);
const daysInObservingMonth = computed(() => new Date(observingMonthDate.value.getFullYear(), observingMonthDate.value.getMonth() + 1, 0).getDate());
const selectedMonthLabel = computed(() => observingMonthDate.value.toLocaleDateString(props.locale, {
  year: 'numeric',
  month: 'short'
}))

function getObservedDayDate(day) {
  return new Date(observingMonthDate.value.getFullYear(), observingMonthDate.value.getMonth(), day);
}

function getDateStyle(day) {
  const date = getObservedDayDate(day);

  const firstWeekday = new Date(observingMonthDate.value.getFullYear(), observingMonthDate.value.getMonth(), 1).getDay();
  const dateWeekday = date.getDay();

  const offsetDate = day + firstWeekday - 1;
  const weekNumber = Math.floor(offsetDate / 7);

  return {
    gridColumn: dateWeekday + 1,
    gridRow: weekNumber + 3,
  }
}

function selectPreviousMonth() {
  observingMonthDate.value = new Date(observingMonthDate.value.getFullYear(), observingMonthDate.value.getMonth() - 1, 1);
}

function selectNextMonth() {
  observingMonthDate.value = new Date(observingMonthDate.value.getFullYear(), observingMonthDate.value.getMonth() + 1, 1);
}

function selectDay(day) {
  selectedDate.value = getObservedDayDate(day);
}

function isDaySelected(day) {
  return observingMonthDate.value.getFullYear() === selectedDate.value.getFullYear() &&
    observingMonthDate.value.getMonth() === selectedDate.value.getMonth() && day === selectedDate.value.getDate();
}
</script>

<style scoped lang="scss">
.calendar {
  display: inline-block;
  border: lightgray 1px solid;
  border-radius: 2px;
  padding: 8px;

  width: 300px;

  &__grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 8px;

    justify-items: center;
  }

  &__month-selector {
    grid-column: 1 / -1;
    justify-self: stretch;
  }

  &__weekday-label {
    text-align: center;
  }

  &__date {
    display: block;
    width: 100%;
    text-align: center;
    cursor: pointer;

    border: transparent 1px solid;

    transition: border-color 100ms ease-out;

    &_selected {
      border: blue 1px solid;
    }
  }
}

.month-selector {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 8px;

  &__arrow {
    display: block;
    height: 12px;

    cursor: pointer;
  }

  &__arrow {
    &_left {
      transform: rotate(180deg);
    }
  }
}
</style>