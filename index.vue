/*! vue3-persian-datepicker v0.1.6 | * MIT License | * Copyright (c) 2021
Mohammad Oftadeh | * https://github.com/mohammadoftadeh/vue3-persian-datepicker
*/
<template>
  <div class="datePicker">
    <input
      type="text"
      :name="name"
      :placeholder="placeholder"
      v-model="state.date"
      ref="inputRef"
      @input="datePickerHandleChange"
      @focus="datePickerHandleShow(true, 'opened')"
      @blur="datePickerHandleBlur"
    />
    <section
      v-if="inline || state.showDatePicker"
      class="datePicker__section"
      @mousedown.prevent
    >
      <table>
        <div v-if="state.showMonthList" class="datePicker__div">
          <button
            type="button"
            v-for="(month, index) in state.rangeNameMonths"
            :key="index"
            :class="{ 'datePicker--active': state.month === ++index }"
            @click="chooseMonth(index)"
          >
            {{ month }}
          </button>
        </div>
        <div
          :class="[
            'datePicker__div',
            !state.showYearList && 'datePicker__div--invisible',
          ]"
        >
          <button
            type="button"
            v-for="(year, index) in state.years"
            :key="index"
            :class="{ 'datePicker--active': state.year === year }"
            :data-scroll="state.year === year ? 1 : ''"
            @click="chooseYear(year)"
          >
            {{ year }}
          </button>
        </div>
        <thead>
          <tr>
            <th class="datePicker__th__button">
              <button type="button" class="" @click="prevMonthHandle">
                ماه قبل
              </button>
            </th>
            <th class="datePicker__th__button">
              <button type="button" @click="state.showMonthList = true">
                {{ state.monthName(state.year, state.month) }}
              </button>
            </th>
            <th>
              <button type="button" @click="openYearList">
                {{ state.year }}
              </button>
            </th>
            <th class="datePicker__th__button">
              <button type="button" @click="nextMonthHandle">ماه بعد</button>
            </th>
          </tr>
          <tr>
            <th v-for="(item, index) in state.weekdaysMin" :key="index">
              {{ item }}
            </th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(item, index) in state.calcNumberWeeks(
              state.offsetDay(state.firstDayMonth(state.year, state.month)),
              state.lastOffsetDay(
                state.lastDayMonth(
                  state.year,
                  state.month,
                  state.daysInMonth(state.year, state.month)
                )
              ),
              state.daysInMonth(state.year, state.month)
            )"
            :key="item"
          >
            <td
              v-for="(day, idx) in state.tableDays(
                state.daysInMonth(state.year, state.month),
                state.prevMonthDays(
                  state.prevMonth(state.year, state.month),
                  state.offsetDay(state.firstDayMonth(state.year, state.month))
                ),
                state.nextMonthDays(
                  state.lastOffsetDay(
                    state.lastDayMonth(
                      state.year,
                      state.month,
                      state.daysInMonth(state.year, state.month)
                    )
                  )
                )
              )[index]"
              :key="idx"
              :class="{
                'datePicker__td--disable': typeof day !== 'number',
                'datePicker__td--active': state.selected === day,
                'datePicker--active':
                  !state.selected &&
                  state.today === day &&
                  state.checkYearMonth(state.year, state.month),
                'datePicker--highlight':
                  state.today === day &&
                  state.checkYearMonth(state.year, state.month),
              }"
              @click="dateSelectedHandle($event, day)"
            >
              {{ day }}
            </td>
          </tr>
          <tr class="datePicker__button">
            <button type="button" @click="goToday">امروز</button>
          </tr>
        </tbody>
      </table>
    </section>
  </div>
</template>

<script>
import PersianDate from "persian-date";
import { computed, onMounted, reactive, ref, onUpdated } from "vue";

export default {
  name: "DatePicker",
  props: ["name", "inline", "format", "placeholder"],
  setup(props, context) {
    const jalal = new PersianDate();

    const inputRef = ref(null);

    const state = reactive({
      year: jalal.year(),
      daysInMonth: computed(() => {
        return (year, month) => {
          if (year && month) {
            return new PersianDate([year, month]).daysInMonth();
          }
        };
      }),
      prevMonth: computed(() => {
        return (year, month) =>
          new PersianDate([year, month <= 1 ? 1 : month - 1]).daysInMonth();
      }),
      nextMonth: computed(() => {
        return (year, month) =>
          new PersianDate([year, month >= 12 ? 1 : month + 1]).daysInMonth();
      }),
      weekdaysMin: jalal.rangeName().weekdaysMin,
      months: jalal.rangeName().months,
      month: jalal.month(),
      today: jalal.date(),
      firstDayMonth: computed(() => {
        return (year, month) => {
          if (year && month) {
            return new PersianDate([year, month, 1]).format("dddd");
          }
        };
      }),
      monthName: computed(() => {
        return (year, month) => {
          if (year && month) {
            return new PersianDate([year, month]).format("MMMM");
          }
        };
      }),
      offsetDay: computed(() => {
        return (firstDayMonth) => {
          switch (firstDayMonth) {
            case "یکشنبه":
              return 1;
            case "دوشنبه":
              return 2;
            case "سه شنبه":
              return 3;
            case "چهار شنبه":
              return 4;
            case "پنج‌شنبه":
              return 5;
            case "جمعه":
              return 6;
            default:
              return 0;
          }
        };
      }),
      lastDayMonth: computed(() => {
        return (year, month, daysInMonth) => {
          if (year && month) {
            return new PersianDate([year, month, daysInMonth]).format("dddd");
          }
        };
      }),
      lastOffsetDay: computed(() => {
        return (lastDayMonth) => {
          switch (lastDayMonth) {
            case "یکشنبه":
              return 5;
            case "دوشنبه":
              return 4;
            case "سه شنبه":
              return 3;
            case "چهار شنبه":
              return 2;
            case "پنج‌شنبه":
              return 1;
            case "شنبه":
              return 6;
            default:
              return 0;
          }
        };
      }),
      prevMonthDays: computed(() => {
        return (prevMonth, offset) => {
          let days = [];
          for (let i = prevMonth; i > prevMonth - offset; i--) {
            days.push(i);
          }
          return days.reverse();
        };
      }),
      nextMonthDays: computed(() => {
        return (offset) => {
          let days = [];
          for (let i = 1; i < offset + 1; i++) {
            days.push(i);
          }
          return days;
        };
      }),
      rangeNameMonths: jalal.rangeName().months,
      years: computed(() => {
        let years = [];
        let start = 1300;
        while (jalal.year() + 20 >= start) {
          years.push(start++);
        }

        return years;
      }),
      selected: null,
      checkYearMonth: computed(() => {
        return (year, month) => {
          return jalal.year() === year && jalal.month() === month;
        };
      }),
      calcNumberWeeks: computed(
        () => (offsetDay, lastOffsetDay, daysInMonth) => {
          return Math.floor((offsetDay + lastOffsetDay + daysInMonth) / 7);
        }
      ),
      tableDays: computed(() => (daysInMonth, prevMonthDays, nextMonthDays) => {
        return genMatrix(daysInMonth, 7, prevMonthDays, nextMonthDays);
      }),
      showYearList: false,
      showMonthList: false,
      showDatePicker: false,
      date: new PersianDate([jalal.year(), jalal.month(), jalal.date()])
        .toLocale("en")
        .format("YYYY/MM/DD"),
    });

    onUpdated(() => {
      state.date = context.attrs.modelValue
        ? context.attrs.modelValue
        : state.date;
    });

    onMounted(() => {
      if (context.attrs.modelValue) {
        const dateArr = context.attrs.modelValue.split("/");
        state.year = Number(dateArr[0]);
        state.month = Number(dateArr[1]);
        state.selected = Number(dateArr[2]);
        state.date = context.attrs.modelValue;
      }
    });

    const prevMonthHandle = () => {
      if (state.month <= 1) {
        state.month = 12;
        state.year -= 1;
        return;
      }

      state.month -= 1;
    };

    const nextMonthHandle = () => {
      if (state.month >= 12) {
        state.month = 1;
        state.year += 1;
        return;
      }
      state.month += 1;
    };

    const dateSelectedHandle = (event = null, index) => {
      if (
        event &&
        event.target.classList["value"].indexOf("datePicker__td--disable") > -1
      )
        return;

      state.selected = index;
      state.date = new PersianDate([state.year, state.month, index])
        .toLocale("en")
        .format(props.format || "YYYY/MM/DD");
      datePickerHandleShow(false, "closed");
      inputRef.value.blur();
      context.emit("update:modelValue", state.date);
      context.emit("selected", state.date);
    };

    const goToday = () => {
      state.year = jalal.year();
      state.month = jalal.month();
      state.today = jalal.date();
      state.selected = state.today;
      dateSelectedHandle(null, state.today);
    };

    const genMatrix = (n, length, firstItems, lastItems) => {
      let result = [];
      let genArrNum = [...firstItems.map((item) => item.toString())];
      for (let i = 1; i <= n; i++) {
        genArrNum.push(i);
      }

      genArrNum = [...genArrNum, ...lastItems.map((item) => item.toString())];

      for (let i = 0; i < genArrNum.length / length; i++) {
        result.push(genArrNum.slice(i * length, i * length + length));
      }

      return result;
    };

    const chooseMonth = (month) => {
      state.month = month;
      state.showMonthList = false;
    };

    const chooseYear = (year) => {
      state.year = year;
      state.showYearList = false;
    };

    const openYearList = () => {
      state.showYearList = true;
      scrollToElementD(".datePicker__div", "button[data-scroll='1']");
    };

    const scrollToElementD = (parent, toEl) => {
      var topPos = document.querySelector(toEl).offsetTop;
      document.querySelectorAll(parent)[0].scrollTop = topPos - 10;
    };

    const datePickerHandleChange = (event) => {
      const dateArr = event.target.value.split("/");

      if (Number(dateArr[1]) > 12) return;

      state.year = Number(dateArr[0]);
      state.month = Number(dateArr[1]);
      state.today = Number(dateArr[2]);
      state.selected = state.today;
      // context.emit("update:modelValue", event.target.value);
      context.emit("input:value", event.target.value);
    };

    const datePickerHandleShow = (flag, emit) => {
      state.showDatePicker = flag;
      context.emit(emit);
    };

    const datePickerHandleBlur = () => {
      datePickerHandleShow(false, "closed");
      state.showYearList = false;
      state.showMonthList = false;
    };

    return {
      inputRef,
      state,
      prevMonthHandle,
      nextMonthHandle,
      dateSelectedHandle,
      goToday,
      chooseMonth,
      chooseYear,
      openYearList,
      datePickerHandleChange,
      datePickerHandleShow,
      datePickerHandleBlur,
    };
  },
};
</script>

<style>
/*! vue3-persian-datepicker v0.1.6 |
 *  MIT License | 
 *  Copyright (c) 2021 Mohammad Oftadeh |
 *  https://github.com/mohammadoftadeh/vue3-persian-datepicker
 */
.datePicker {
  position: relative;
  width: fit-content;
  direction: rtl;
}
.datePicker input {
  border: 1px solid #333;
  padding: 0.5rem;
  border-radius: 0.5rem;
  font-size: 1.3rem;
  text-align: left;
}

.datePicker button {
  border: none;
  outline: none;
  background: none;
  cursor: pointer;
}

.datePicker .datePicker__section {
  position: absolute;
  top: 110%;
  left: 50%;
  transform: translateX(-50%);
  box-shadow: 0 0 15px -10px rgba(0, 0, 0, 0.3);
  border: 1px solid #eee;
  z-index: 999;
}
.datePicker table {
  position: relative;
  background: #fff;
}

.datePicker table tr th {
  font-weight: normal;
}

.datePicker table thead tr:last-child {
  margin-bottom: 0.5rem;
  color: #979ca6;
}

.datePicker table thead tr:first-child th:first-child,
.datePicker table thead tr:first-child th:last-child {
  margin: 0 0.25rem;
}

.datePicker table thead tr:first-child th button {
  transition: 0.3s;
}

.datePicker table thead tr:first-child th:first-child button,
.datePicker table thead tr:first-child th:last-child button {
  background: #efefef;
  border-radius: 5px;
}

.datePicker table thead tr:first-child th:first-child button:hover,
.datePicker table thead tr:first-child th:last-child button:hover {
  background: #e6e4e4;
}

.datePicker table thead tr:first-child th:nth-child(2) button {
  margin-top: 0.3rem;
  font-size: 17px;
  font-weight: bold;
}

.datePicker table thead tr:first-child th:nth-child(3) button {
  font-size: 17px;
}

.datePicker table tbody td.datePicker__td--disable {
  color: #d2d6dc;
}

.datePicker table tr {
  display: flex;
  padding: 0 0.3rem;
}

.datePicker table tr .datePicker__th__button button {
  font-size: 13px;
  padding: 0.1rem 0.3rem;
}

.datePicker table tr td,
.datePicker table tr th:not(.datePicker__th__button):not(.datePicker__th) {
  width: 30px;
  height: 30px;
  line-height: 30px;
  text-align: center;
  transition: 0.1s;
  font-weight: normal;
  border-radius: 300px;
}

.datePicker table tr td:hover:not(th):not(.datePicker__td--disable),
.datePicker
  table
  tr
  th:not(.datePicker__th__button):not(.datePicker__th):hover:not(th):not(.datePicker__td--disable) {
  background: #ffa64d;
  color: #fff;
  cursor: pointer;
}

.datePicker table thead tr:first-child {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin: 0.5rem 0;
}

.datePicker table .datePicker__div {
  position: absolute;
  content: "";
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: #fff;
  padding: 0.3rem;
  z-index: 9999;
  overflow-y: auto;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
}

.datePicker table .datePicker__div button {
  margin: 0.2rem 0.1rem;
  width: 60px;
  font-size: 15px;
  padding: 0.3rem 0.2rem;
  text-align: center;
  border-radius: 5px;
  transition: 0.13s;
}
.datePicker table .datePicker__div button:hover {
  background: #ffa64d;
  color: #fff;
}

.datePicker table tr td.datePicker--highlight {
  background: #fcc996;
}

.datePicker .datePicker--active {
  background: #ffa64d !important;
  color: #fff;
}

.datePicker table tr .datePicker__td--active {
  background: #ffa64d !important;
  color: #fff;
}

.datePicker .datePicker__div--invisible {
  visibility: hidden;
  opacity: 0;
}

.datePicker .datePicker__button {
  margin: 0.75rem 0.25rem 0.5rem;
}

.datePicker .datePicker__button button {
  background: #ffa64d !important;
  color: #fff;
  width: 100%;
  padding: 0.25rem 0;
  border-radius: 5px;
}
</style>
